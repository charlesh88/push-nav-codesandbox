<template>
  <h2 class="l-view">
    <div class="l-view__label">{{ object.label }}</div>
    <button @click="syncNav()">Sync</button>
  </h2>
</template>
<script>
export default {
  name: "ObjectView",
  props: {
    object: Object,
    required: true
  },
  methods: {
    syncNav() {
      console.log("syncNav", this.object.id);
      this.$emit("sync-nav", this.object.id);
    }
  }
};
</script>