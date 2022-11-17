<template>
  <div>
    <!--    Tabs-->
    <div style="display: flex;justify-content: space-between">
      <button v-for="tab in tabs" :key="tab.name">{{ tab.name }}</button>
      <!--      <button @click="show_tab_handler('annotations')">Annotations</button>-->
      <!--      <button @click="show_tab_handler('training')">Training</button>-->
      <!--      <button @click="show_tab_handler('details')">Details</button>-->
    </div>
    <hr>
    <!--    <annotations-->
    <!--        v-if="tabs.annotations"-->

    <!--        :pspdfkit-instance="PSPDFKitInstance"-->
    <!--        :annotations="annotations"-->
    <!--        :labels="labels"-->

    <!--        @delete-annotation="delete_annotation_handler"-->
    <!--        @edit-annotation="edit_annotation_handler"-->
    <!--        @add-annotation="add_annotation_handler"-->
    <!--    />-->

    <!--    <al-training-->
    <!--        v-if="tabs.training"-->

    <!--        :labels="labels"-->

    <!--        @edit-label="edit_label_handler"-->
    <!--    />-->


    <!--    <al-details-->
    <!--        v-if="tabs.details"-->

    <!--        :details="details"-->

    <!--        @submit-clicked="submit_clicked_handler"-->
    <!--    />-->

    <al-activity v-if="tabs.activity"/>
  </div>
</template>

<script>
import AlActivity from "@/components/AlActivity";

export default {
  name: "CustomSidebar",
  components: {AlActivity},
  data: function () {
    return {
      tagType: {
        selected: true,
        value: 'term'
      },
      tabs: {
        annotations: {
          name: 'Annotations',
          active: false
        },
        training: {
          name: 'Training',
          active: false
        },
        details: {
          name: 'Details',
          active: false
        },
        activity: {
          name: 'Activity',
          active: true
        }
      }
    }
  },
  props: {
    PSPDFKitInstance: Object,
    annotations: Array,
    details: Array,
    labels: Array
  },
  methods: {
    click_handler: function (tagType) {
      console.log(tagType)

      tagType.selected = !tagType.selected

    },

    // Annotations
    delete_annotation_handler(id) {
      this.$emit('delete-annotation', id)
    },
    edit_annotation_handler(annotation, opts) {
      this.$emit('edit-annotation', annotation, opts)
    },
    add_annotation_handler(annotation) {
      this.$emit('add-annotation', annotation)
    },

    // Labels
    edit_label_handler(label, opts) {
      this.$emit('edit-label', label, opts)
    },

    // details
    submit_clicked_handler: function (inputs) {
      this.$emit('edit-details', inputs)
    },

    show_tab_handler: function (tab) {
      Object.keys(this.tabs).forEach(tab => this.tabs[tab] = false)
      this.tabs[tab] = true;
    }
  }
}
</script>

<style scoped>

</style>