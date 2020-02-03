<template>
  <h2 class="l-view">
    <div class="l-view__label">{{ object.label }}</div>
  </h2>
</template>
<script>
export default {
  name: "ObjectView",
  props: {
    object: Object,
    required: true
  }
};
</script>