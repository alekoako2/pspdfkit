<template>
  <div style="display: flex;flex-direction: column;gap: 16px">
    <Annotation
        v-for="annotation in annotations"

        :annotation="annotation"
        :key="annotation.id"

        style="border: 1px solid black;"

        @enable-annotation-editor="enable_annotation_editor_handler"
        @delete-annotation="delete_annotation_handler"
        @add-annotation="add_annotation_handler"
    />
  </div>
</template>

<script>
import Annotation from "@/components/Annotation";

export default {
  name: "Annotations",
  components: {Annotation},
  props: {
    annotations: Array
  },
  methods: {
    enable_annotation_editor_handler(id) {
      this.$emit('enable-annotation-editor', id)
    },
    delete_annotation_handler(id) {
      this.$emit('delete-annotation', id)
    },
    add_annotation_handler(annotation) {
      this.$emit('add-annotation', annotation)
    }
  }
}
</script>

<style scoped>

</style>