<template>
  <div>
    <a v-show="!annotation.active" href="#" style="display: block" @click="annotation_clicked_handler(annotation)">
      <div style=";display: flex;justify-content: space-between">
        <span>{{ annotation.label }}</span>
        <span>></span>
      </div>
    </a>

    <!--        inner body-->
    <div v-if="annotation.active">
      <span>Add annotation</span> <br>
      <label for="label-input">
        Label <br>
        <input id="label-input" :value="annotation.label" type="text" ref="annotationLabelInput">
      </label>
      <p>
        {{ annotation.text }} <a href="#">more</a>
      </p>
      <a href="#" @click="edit_annotation_handler(annotation)">
        Edit Highlight
      </a>
      <hr>
      <div style="display: flex;justify-content: center;gap: 16px">
        <a href="#" @click="annotation_add_cancelled_handler(annotation)">
          Cancel
        </a>
        <a href="#" @click="add_annotation_handler(annotation)">
          Add
        </a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Annotation",
  props: {
    annotation: Object
  },
  methods: {
    annotation_add_cancelled_handler: function (annotation) {
      if (annotation.initial) {
        this.$emit('delete-annotation', annotation.id)
        return;
      }
      annotation.active = false;
    },
    annotation_clicked_handler: function (annotation) {
      annotation.active = true;
    },
    edit_annotation_handler: function (annotation) {
      this.$emit('enable-annotation-editor', annotation.id)
    },
    add_annotation_handler: function (annotation) {
      this.$emit('add-annotation', {...annotation, label: this.$refs.annotationLabelInput.value})
    }
  }
}
</script>

<style scoped>

</style>