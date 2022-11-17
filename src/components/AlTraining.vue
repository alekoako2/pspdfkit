<template>
  <div>
    <div>
      <button v-on:click="show = !show">
        Toggle
      </button>
      <transition name="fade">
        <p v-if="show">hello</p>
      </transition>
      <div v-for="filter in filters" :key="filter.name">
        <h3 @click="filter_click_handler(filter.name)">{{ filter.name }}</h3>
        <transition name="fade">
          <div v-if="filter.active">
            aleko
            <!--            <div v-for="filterOption in filter.options" :key="filterOption+filter.name"> {{ filterOption }}</div>-->
          </div>
        </transition>
      </div>
    </div>

    <h3>AI Labels</h3>
    <AlCard
        v-for="label in labels"

        :key="label.id"
        :active="label.active"

        @header-clicked="card_header_clicked_handler(label)"
    >
      <template v-slot:header>
        <div style="display: flex;justify-content: space-between;width: 100%">
          <div>{{ label.title }}</div>
          <div style="border: black 1px solid">{{ label.status }}</div>
        </div>
      </template>

      <template v-slot:content>
        <al-label
            :label="label"
        />
      </template>
    </AlCard>
  </div>
</template>

<script>
import AlLabel from "@/components/AlLabel";
import AlCard from "@/components/AlCard";

export default {
  name: "AlTraining",
  components: {AlCard, AlLabel},
  data: function () {
    return {
      show: false,
      filters: [
        {
          name: 'filter1',
          active: false,
          options: [
            '1',
            '2',
            '3'
          ]
        },

        {
          name: 'filter2',
          active: false,
          options: [
            '1',
            '2',
            '3'
          ]
        }
      ]
    }
  },
  props: {
    labels: Array
  },
  methods: {
    filter_click_handler: function (name) {
      const found = this.filters.find((filter) => filter.name === name);
      found.active = !found.active;
    },
    card_header_clicked_handler: function (label) {
      this.$emit('edit-label', label, {active: !label.active})
    },
  }
}
</script>

<style scoped>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}

.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */
{
  opacity: 0;
}
</style>