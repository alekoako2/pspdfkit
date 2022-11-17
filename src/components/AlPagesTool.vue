<template>
  <div style="display: flex">
    <span>Page</span>
    <input
        type="number"
        min="1"

        :value="currentPage1"
        :max="pspdfkitInstance?.totalPageCount"

        @change="pages_input_change_handler"
    />
    <span>of {{ pspdfkitInstance?.totalPageCount }}</span>
    <button @click="next_page_click_handler">▼</button>
    <button @click="previous_page_click_handler">▲</button>
    {{ currentPage1 }}
  </div>
</template>

<script>
export default {
  name: "PagesTool",
  props: {
    pspdfkitInstance: Object
  },
  data: function () {
    return {
      currentPage: 1
    }
  },
  computed: {
    currentPage1: function () {
      return this.pspdfkitInstance?.viewState.currentPageIndex
    }
  },
  mounted() {
  },
  methods: {
    previous_page_click_handler: function () {
      this.pspdfkitInstance.setViewState((state) => state.goToPreviousPage())
      if (this.currentPage > 1)
        this.currentPage--;
      this.$forceUpdate();


    },
    next_page_click_handler: function () {


      this.pspdfkitInstance.setViewState((state) => state.goToNextPage())
      if (this.currentPage < 15)
        this.currentPage++;

      this.pspdfkitInstance.viewState = {...this.pspdfkitInstance.viewState};
      this.$forceUpdate();
    },

    pages_input_change_handler: function ({target}) {
      const inputValue = +target.value;
      this.currentPage = inputValue > 15 ? 15 : inputValue < 1 ? 1 : inputValue;

      this.pspdfkitInstance.setViewState(
          this.pspdfkitInstance.viewState.set(
              'currentPageIndex',
              this.currentPage - 1
          )
      );

    },
  }
}
</script>

<style scoped>

</style>