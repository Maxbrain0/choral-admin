<template>
  <section class="section">
    <div class="container">
      <div class="columns is-mobile is-centered">
        <div class="column is-three-fifths-tablet is-two-fifths-desktop">
          <h1 class="title has-text-centered has-text-weight-bold">Edit Admin</h1>
          <div class="buttons is-centered">
            <router-link
            :to="{name: 'manageAdmins'}"
            tag="button"
            class="button is-warning">Back</router-link>
          </div>
          <div v-if="loading" class="has-text-centered">
            <span
              class="icon is-large has-text-info has-text-centered">
              <i class="fas fa-circle-notch fa-spin fa-3x"></i>
            </span>
          </div>
          <form v-if="!loading" @submit.prevent="onEditAdmin">
            <div class="field">
              <label class="label">Name</label>
              <div class="control has-icons-left">
                <input
                  :class="{'input': true, 'is-danger': errors.has('name') }"
                  name="name"
                  v-model="adminToEdit.name"
                  data-vv-delay="500"
                  v-validate="'required|min:2'"
                  placeholder="Full Name">
                <span class="icon is-small is-left">
                  <i class="fas fa-user"></i>
                </span>
                <p
                  v-if="errors.has('name')"
                  class="help is-danger">{{ errors.first('name') }}</p>
              </div>
            </div>
            <div class="field">
              <label class="label">Role</label>
              <div class="control has-icons-left">
                <input
                  :class="{'input': true, 'is-danger': errors.has('role') }"
                  name="role"
                  v-model="adminToEdit.role"
                  data-vv-delay="500"
                  v-validate="'required|min:2'"
                  placeholder="Role">
                <span class="icon is-small is-left">
                  <i class="fas fa-user-tie"></i>
                </span>
                <p
                  v-if="errors.has('role')"
                  class="help is-danger">{{ errors.first('role') }}</p>
              </div>
            </div>
            <div class="field">
              <label class="label">Email</label>
              <div class="control has-icons-left">
                <input
                  :class="{'input': true, 'is-danger': errors.has('email') }"
                  name="email"
                  v-model="adminToEdit.email"
                  type="email"
                  data-vv-delay="500"
                  v-validate="'required|email'"
                  placeholder="Email Address">
                <span class="icon is-small is-left">
                  <i class="fas fa-envelope"></i>
                </span>
                <p
                  v-if="errors.has('email')"
                  class="help is-danger">{{ errors.first('email') }}</p>
              </div>
            </div>
            <div v-if="adminToEdit.downloadURL">
              <label class="label">Current Profile Image</label>
              <figure class="image">
                <img class="is-rounded" :src="adminToEdit.downloadURL" alt="Current Image">
              </figure>
              <div class="buttons is-centered">
                <button
                class="button"
                @click.prevent="removeImage">Remove Image</button>
              </div>
            </div>
            <div class="field file has-name is-boxed is-fullwidth">
              <label class="file-label">
              <input
                class="file-input"
                type="file"
                name="profileImageFile"
                accept="image/x-png,image/jpeg"
                @change="processFile($event)">
              <span class="file-cta">
                <span class="file-icon">
                  <i class="fas fa-upload"></i>
                </span>
                <span class="file-label">
                  New Profile Image
                </span>
              </span>
              <span class="file-name">
                {{ profileImageFile.name }}
              </span>
              </label>
            </div>
            <div>
              <vue-croppie
                v-show="profileImageFile"
                ref="croppieRef"
                :viewport="{ width: 256, height: 256, type: 'circle' }"
                :boundary="{ width: 256, height: 256 }"
                :quality="1"
                :enable-resize="false">
              </vue-croppie>
            </div>
            <div class="buttons is-centered">
              <button class="button is-info"
              :disabled="errors.any() || hasInvalidInput"
              type="submit">Save Changes</button>
              <button
              @click.prevent="confirmDelete"
              class="button is-danger">Delete Admin</button>
            </div>
          </form>
        </div>
      </div>
    </div>
    <div :class="{ 'modal': true, 'is-active': showModal }">
      <div @click.prevent="clearModal" class="modal-background"></div>
      <div class="modal-content">
        <div class="card">
          <header class="card-header">
            <p class="card-header-title">Are you sure?</p>
          </header>
          <div class="card-content">
          <div class="content">
            You cannot undo this delete. All admin information will be lost.
            </div>
          </div>
          <footer class="card-footer">
            <a href="#"
            @click.prevent="onDeleteAdmin"
            class="card-footer-item has-text-danger has-text-weight-bold">Confirm Delete</a>
            <a @click.prevent="clearModal"
            href="#"
            class="card-footer-item has-text-weight-bold">Cancel</a>
          </footer>
        </div>
      </div>
      <button @click.prevent="clearModal" class="modal-close is-large" aria-label="close"></button>
    </div>
  </section>
</template>

<script>
export default {
  props: ['id'],
  data() {
    return {
      profileImageFile: '', // initialize as string even though will store a file
      newProfileImageUrl: '',
      showModal: false,
    };
  },
  methods: {
    onEditAdmin() {
      const croppedOptions = {
        type: 'blob',
        format: 'jpeg',
        circle: true,
        size: 'viewport',
      };
      let croppedImageFile = null;

      this.$refs.croppieRef.result(croppedOptions)
        .then((output) => {
          if (output) {
            croppedImageFile = new File([output], this.profileImageFile.name, {
              type: 'image/jpeg',
            });
          }

          const updatedAdmin = {
            adminId: this.id,
            updatedName: this.adminToEdit.name,
            updatedEmail: this.adminToEdit.email,
            updatedRole: this.adminToEdit.role,
            updatedImageFile: croppedImageFile,
          };
          this.$store.dispatch('adminModule/editAdmin', updatedAdmin);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    onDeleteAdmin() {
      this.$store.dispatch('adminModule/deleteAdmin', this.id);
    },
    confirmDelete() {
      this.showModal = true;
    },
    clearModal() {
      this.showModal = false;
    },
    removeImage() {
      this.$store.dispatch('adminModule/removeImage', this.id);
    },
    processFile(event) {
      const [file] = event.target.files;
      this.profileImageFile = file; // uses array destructuring cuz airbnb

      const reader = new FileReader();

      reader.addEventListener('load', () => {
        this.newProfileImageUrl = reader.result;
        this.$refs.croppieRef.bind({
          url: this.newProfileImageUrl,
        });
      });

      reader.readAsDataURL(file);
    },
  },
  computed: {
    hasInvalidInput() {
      // if any element is pristine, don't disable submit button
      return Object.keys(this.fields).some(key => this.fields[key].invalid);
    },
    adminToEdit() {
      const admins = this.$store.getters['adminModule/adminList'];
      const admin = admins.filter(a => a.id === this.id);
      return admin[0].data;
    },
    loading() {
      return this.$store.getters['adminModule/loading'];
    },
  },
  beforeCreate() {
    if (this.$store.getters['adminModule/adminList'].length === 0) {
      this.$store.dispatch('adminModule/loadAdmins');
    }
  },
};
</script>

<style lang="scss" scoped>
  .file {
    margin-top: 2em;
    margin-bottom: 2em;
  }

  figure {
    img {
      max-width: 128px;
      margin: auto;
    }
  }

  .file-label, .file-name {
    text-align: center;
  }
</style>
