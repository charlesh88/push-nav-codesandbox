<template>
  <h2 class="l-view">{{ object.label }}</h2>
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