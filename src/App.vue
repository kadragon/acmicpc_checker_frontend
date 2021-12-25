<template>
  <div id="app">
    <navbar />
    <router-view />
  </div>
</template>

<script>
import navbar from "./components/layout/Navbar.vue";

export default {
  name: "App",
  data: function() {
    return {};
  },
  components: { navbar },
};
</script>

<style></style>
