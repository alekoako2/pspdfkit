<template>
  <div
      style="border: 1px solid black;padding: 10px;margin-bottom: 10px"
  >
    <a style="display: block" href="#" @click="header_click_handler">
      <div style="display: flex;justify-content: space-between">
        <slot name="header"/>

        <span style="margin-left: 5px">{{ active ? '▼' : '>' }}</span>
      </div>
    </a>
    <div v-if="active">
      <slot name="content"/>
    </div>
  </div>
</template>

<script>
export default {
  name: "AlCard",
  props: {
    active: Boolean
  },
  methods: {
    header_click_handler: function () {
      this.$emit('header-clicked');
    }

  }
}
</script>

<style scoped>

</style>