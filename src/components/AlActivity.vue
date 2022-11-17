<template>
  <div>
    activities
    <div>
      <div v-for="comment in comments" :key="comment.message" style="border: 1px solid black">
        <div>
          <div>Avatar {{ comment.author }}</div>
          <div>More Icon</div>
        </div>
        <div v-html="comment.message"></div>
        <div>{{ comment.created }}</div>
      </div>
    </div>
  </div>
</template>

<script>

export default {

  name: "AlActivity",
  components: {},
  props: {
    comments: Array
  },
}
</script>

<style scoped>

</style>