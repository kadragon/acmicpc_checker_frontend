<template>
  <h4>과제 목록</h4>
  <add />

  <list />
</template>

<script>
import list from "./List.vue";
import add from "./AddAssign.vue";

export default {
  name: "assignment",
  components: { list, add },
  data() {
    return {};
  },
  methods: {},
};
</script>

<style></style>
