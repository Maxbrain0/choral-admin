<template>
  <section class="section">
    <div class="container">
      <h1 class="title has-text-centered has-text-weight-bold">Manage Performances</h1>
      <div class="has-text-centered">
        <router-link
          class="button is-medium has-text-centered is-danger"
           :to="{name: 'createPerformance'}">
          <span class="icon">
            <i class="fas fa-plus-circle"></i>
          </span>
          <span>New Performance</span>
        </router-link>
      </div>
      <div v-if="loading" class="has-text-centered">
        <span
          class="icon is-large has-text-info has-text-centered">
          <i class="fas fa-circle-notch fa-spin fa-3x"></i>
        </span>
      </div>
      <div v-if="!loading" class="columns is-tablet is-multiline">
        <div
        class="column is-half-tablet"
        v-for="performance in performanceList"
        :key="performance.id">
          <div class="card">
            <div v-if="performance.data.downloadURL" class="card-image">
              <figure class="image is-4by3">
                <img :src="performance.data.downloadURL" alt="Placeholder image">
              </figure>
            </div>
            <div v-else class="card-image">
              <figure class="image is-4by3">
                <img
                src="@/assets/rect_filler.svg"
                alt="Placeholder image">
              </figure>
            </div>
            <div class="card-content">
              <div class="content">
                <div
                  class="has-text-center is-size-5 has-text-link has-text-weight-bold
                  content-item-title">Title
                </div>
                <div class="is-size-6 content-item">{{performance.data.eventTitle}}</div>
              </div>
            </div>
            <div class="card-content">
              <div class="content">
                <div
                  class="has-text-center is-size-5 has-text-link has-text-weight-bold
                  content-item-title">Date
                </div>
                <div class="is-size-6 content-item">{{performance.data.dateUTC}}</div>
              </div>
            </div>
            <div class="card-content">
              <div class="content">
                <div
                  class="has-text-center is-size-5 has-text-link has-text-weight-bold
                  content-item-title">Venue
                </div>
                <div class="is-size-6 content-item">{{performance.data.venueName}}</div>
              </div>
            </div>
            <div class="card-content">
              <div class="content">
                <div
                  class="has-text-center is-size-5 has-text-link has-text-weight-bold
                  content-item-title">Address
                </div>
                <div
                  class="content-item"
                  v-if="performance.data.address1">
                  {{performance.data.address1}}
                </div>
                <div
                  class="content-item"
                  v-if="performance.data.address2">
                  {{performance.data.address2}}
                </div>
                <div
                  class="content-item"
                  v-if="performance.data.city">
                  {{performance.data.city}}
                </div>
                <div class="content-item">
                  <span v-if="performance.data.state">{{performance.data.state}}</span>
                  <span v-if="performance.data.postalCode">, {{performance.data.postalCode}}</span>
                </div>
              </div>
            </div>
            <div class="card-content">
              <div class="content">
                <div
                  class="has-text-center is-size-5 has-text-link has-text-weight-bold
                  content-item-title">Note
                </div>
                <div class="is-size-6 content-item">{{performance.data.note}}</div>
              </div>
            </div>
            <div class="card-content">
              <div class="content">
                <div
                  class="has-text-center is-size-5 has-text-link has-text-weight-bold
                  content-item-title">Map Link
                </div>
                <div class="is-size-6 content-item">
                  <a
                    :href="performance.data.mapURL"
                    target="_blank">
                    {{performance.data.mapURL}}
                  </a>
                </div>
              </div>
            </div>
            <footer class="card-footer">
              <router-link
                :to="{name: 'editPerformance', params: {id: performance.id}}"
                class="card-footer-item">Edit
              </router-link>
            </footer>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
    };
  },
  computed: {
    loading() {
      return this.$store.getters['performanceModule/loading'];
    },
    performanceList() {
      return this.$store.getters['performanceModule/performanceList'];
    },
  },
  created() {
    this.$store.dispatch('performanceModule/loadPerformances');
  },
};
</script>

<style lang="scss" scoped>
  figure {
    margin: auto;
  }

  .card-content {
    padding-top: 0;
    padding-bottom: 0;
  }
  .content-item-title {
    margin-top: 0;
    margin-bottom: 0;
  }

  .content-item {
    margin-top: 0;
    margin-bottom: 0;
  }
</style>
