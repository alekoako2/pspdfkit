<template>
  <div style="display: flex;flex-direction: column;gap: 16px">

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

    <div style="width: 100%" @click="click1Handler">
      aleko
      <button @click.stop="click2Handler">kaki</button>
    </div>


    <!--    Annotations-->
    <div style="display: flex;flex-direction: column;gap: 16px">
      <AlCard
          v-for="annotation in annotations"

          :key="annotation.id"
          :active="annotation.active"

          @header-clicked="card_header_clicked_handler(annotation)"
      >
        <template v-slot:header>
          <span>{{ annotation.label.title }}</span>
        </template>

        <template v-slot:content>
          <Annotation
              :annotation="annotation"
              :labels="labels"

              @enable-annotation-editor="enable_annotation_editor_handler"
              @delete-annotation="delete_annotation_handler"
              @edit-annotation="edit_annotation_handler"
              @add-annotation="add_annotation_handler"
          />
        </template>
      </AlCard>
    </div>
  </div>
</template>

<script>
import Annotation from "@/components/Annotation";
import AlCard from "@/components/AlCard";

export default {
  name: "Annotations",
  components: {AlCard, Annotation},
  props: {
    annotations: Array,
    pspdfkitInstance: Object,
    labels: Array
  },
  methods: {
    enable_annotation_editor_handler(id) {
      this.pspdfkitInstance.setEditingAnnotation(id)
    },
    delete_annotation_handler(id) {
      this.$emit('delete-annotation', id)
    },
    edit_annotation_handler(annotation) {
      this.$emit('edit-annotation', annotation)
    },
    add_annotation_handler(annotation) {
      this.$emit('add-annotation', annotation)
    },

    card_header_clicked_handler: function (annotation) {
      this.$emit('edit-annotation', annotation, {active: !annotation.active})
      if (!annotation.active) this.pspdfkitInstance.setEditingAnnotation(annotation.id)
    },

    click1Handler: function () {
      console.log('aleko')
    },
    click2Handler: function () {
      console.log('nini')
    }
  }
}
</script>

<style scoped>

</style>