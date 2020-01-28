<template>
  <div class="l-view">This is l-view {{ object.label }}</div>
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