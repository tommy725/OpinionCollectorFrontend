<template>
  <div class="sidepanel d-flex flex-column flex-shrink-0 p-3 bg-light" style="width: 280px; height: 92vh"> 
    <a class="mb-3 mb-md-0 me-md-auto link-dark text-decoration-none mx-auto">
      <h1 class="fs-4">Dashboard</h1>
    </a>
    <hr />
    <ul class="nav nav-pills flex-column mb-auto">
      <li class="nav-item">
        <router-link class="nav-link" :class="itemClasses('panel')" :to="{ name: 'Panel' }">
          Data
        </router-link>
      </li>
      <li>
        <router-link class="nav-link" :class="itemClasses('opinions')" :to="{ name: 'Opinions' }">
          Opinions
        </router-link>
      </li>
      <li>
        <router-link class="nav-link" :class="itemClasses('suggestions')" :to="{ name: 'Suggestions' }">
          Suggestions
        </router-link>
      </li>
    </ul>
    <hr />
  </div>
</template>

<script>
export default {
  methods: {
    itemClasses(route) {
      const classes = ["nav-item"];
      if (this.currentRoute === route) {
        classes.push("active");
      } else {
        classes.push("link-dark");
      }
      return classes;
    },
  },
  computed: {
    currentRoute() {
      return this.$route.name.toLowerCase();
    },
  },
};
</script>

<style scoped>
@media (max-width: 1200px) {
  .sidepanel {
    display: none !important;
  }
}
</style>
