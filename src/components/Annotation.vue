<template>
  <div>
    <template v-if="annotation.initial">
      <span>Add annotation</span> <br>
    </template>
    <label for="label-select">
      Label <br>
      <select
          name=""
          id="label-select"
          :value="annotation.label.id"
          @change="label_change_handler"
      >
        <option v-for="{id,title} in labels" :value="id" :key="id">
          {{ title }}
        </option>
      </select>
    </label>
    <p>
      {{ annotation.text }} <a href="#">more</a>
    </p>
    <a href="#" @click="annotation_edit_highlight_click_handler()">
      Edit Highlight
    </a>
    <hr>
    <div style="display: flex;justify-content: center;gap: 16px">
      <template v-if="annotation.initial">
        <a href="#" @click="annotation_cancel_click_handler()">
          Cancel
        </a>
        <a href="#" @click="annotation_add_edit_click_handler()">
          Add
        </a>
      </template>
      <template v-else>
        <a href="#" @click="annotation_remove_click_handler()">
          Remove
        </a>
        <a href="#" @click="annotation_add_edit_click_handler()">
          Edit Tag
        </a>
      </template>
    </div>
  </div>
</template>

<script>
export default {
  name: "Annotation",
  props: {
    annotation: Object,
    labels: Array
  },
  data: function () {
    return {
      selectedLabel: this.annotation.label
    }
  },
  methods: {
    annotation_edit_highlight_click_handler: function () {
      this.edit_highlight();
    },
    annotation_cancel_click_handler: function () {
      if (this.annotation.initial) {
        this.delete_annotation();
        return;
      }

      this.annotation.active = false;
    },
    annotation_remove_click_handler: function () {
      this.delete_annotation();
    },
    annotation_add_edit_click_handler: function () {
      this.$emit('edit-annotation', {...this.annotation, label: this.selectedLabel, initial: false})
    },

    label_change_handler: function ($e) {
      this.selectedLabel = this.labels.find(label => label.id === +$e.target.value)
    },

    // Helpers
    edit_highlight: function () {
      this.$emit('enable-annotation-editor', this.annotation.id)
    },
    delete_annotation: function () {
      this.$emit('delete-annotation', this.annotation.id)
    }
  }
}
</script>

<style scoped>

</style>