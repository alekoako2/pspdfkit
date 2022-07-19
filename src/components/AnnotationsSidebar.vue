<template>
  <div>

    <!--    Header-->
    <div>
      Annotations (3)
    </div>
    <hr>

    <!--    Sort-->
    <div style="display: flex;justify-content: space-between">
      <div>
      <span>
        Sort:
      </span>
        <select id="" name="">
          <option value="position">Position</option>
        </select>
      </div>
      <button>sort</button>
    </div>


    <!--    Annotations-->
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

  </div>
</template>

<script>
import Annotation from "@/components/Annotation";

export default {
  name: "AnnotationsSidebar",
  components: {Annotation},
  data: function () {
    return {}
  },
  props: {
    PSPDFKitInstance: Object,
    annotations: Array
  },
  methods: {
    enable_annotation_editor_handler(id) {
      this.PSPDFKitInstance.setEditingAnnotation(id)
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