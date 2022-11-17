<template>
  <div>
    <div style="display: flex;justify-content: space-between">
      <div style="display: flex;flex-direction: column;align-items: center">
        <span>0.98</span>
        <span>F1 score</span>
      </div>
      <div style="display: flex;flex-direction: column;align-items: center">
        <span>0.98</span>
        <span>Precision</span>
      </div>
      <div style="display: flex;flex-direction: column;align-items: center">
        <span>0.98</span>
        <span>Recall</span>
      </div>
    </div>
    <div>
      {{ label.description }}
    </div>
    <div>
      <strong>Projects:</strong>
      <span v-for="project in label.projects" :key="project">{{ project }}</span>
    </div>
    <div>
      <button>Test in CLM</button>
      <button>Publish</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "AlLabel",
  props: {
    label: Object
  }
}
</script>

<style scoped>

</style>