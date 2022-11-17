<template>
  <div>
    <div v-if="showSearchInput" style="display: flex">
      <input
          type="text"
          placeholder="force majeure"

          :value="searchInputValue"

          @change="search_input_change_handler"
          @keyup.enter="search_input_keyup_enter_handler"
      >
      <button @click="search_click_handler">S ICON</button>
      <div style="display: flex" v-if="searchResults.size>0">
        <button @click="clear_results">clear</button>
        <button @click="previous_search_result_click_handler">▲</button>
        <span>{{ focusedResultIndex }}/{{ searchResults.size }}</span>
        <button @click="next_search_result_click_handler">▼</button>
      </div>
    </div>
    <button
        v-if="!showSearchInput"

        @click="show_search_input_click_handler"
    >
      Search
    </button>
  </div>
</template>

<script>
import PSPDFKit from "pspdfkit";

export default {
  name: "AlSearchTool",
  props: {
    pspdfkitInstance: Object
  },
  data: function () {
    return {
      showSearchInput: false,
      recentSearchInputs: [],
      searchInputValue: '',
      searchResults: {},
      focusedResultIndex: null
    }
  },
  methods: {
    //Handlers
    clear_results: function () {
      this.pspdfkitInstance.setSearchState(this.pspdfkitInstance.searchState.set("results", PSPDFKit.Immutable.List()));
    },
    previous_search_result_click_handler: function () {
      let {focusedResultIndex} = this.pspdfkitInstance.searchState;
      this.select_search_result(
          focusedResultIndex === 0 ? this.searchResults.size - 1 : focusedResultIndex - 1
      )
    },
    next_search_result_click_handler: function () {
      let {focusedResultIndex} = this.pspdfkitInstance.searchState;
      this.select_search_result(
          focusedResultIndex === this.searchResults.size - 1 ? 0 : focusedResultIndex + 1
      )
    },

    show_search_input_click_handler: function () {
      this.showSearchInput = 1;
    },
    search_input_change_handler: function ({target}) {
      this.searchInputValue = target.value;
    },

    search_input_keyup_enter_handler: function () {
      this.perform_search()
    },
    search_click_handler: function () {
      this.perform_search()
    },

    // Helpers
    perform_search: async function () {
      if (this.recentSearchInputs.find((search) => search === this.searchInputValue)) return;

      this.recentSearchInputs.push(this.searchInputValue);

      // const results = await this.PSPDFKitInstance.search(`^Lorem ipsum dolor sit.*Fusce es`, {searchType: PSPDFKit.SearchType.REGEX});

      this.searchResults = await this.pspdfkitInstance.search(`Lorem ipsum dolor sit.*Fusce`, {searchType: PSPDFKit.SearchType.REGEX});

      console.log(this.searchResults)

      this.select_search_result(0)

      this.pspdfkitInstance.setSearchState(this.pspdfkitInstance.searchState.set("results", this.searchResults));
    },
    select_search_result: function (index) {
      const searchState = this.pspdfkitInstance.searchState;
      let newSearchState = searchState.set('focusedResultIndex', index);
      newSearchState = newSearchState.set('isFocused', true);
      this.pspdfkitInstance.setSearchState(newSearchState)

      const pageIndex = this.searchResults.get(index).pageIndex
      this.pspdfkitInstance.setViewState(
          this.pspdfkitInstance.viewState.set(
              'currentPageIndex',
              pageIndex
          )
      );

      this.focusedResultIndex = index + 1;
    }
  }
}
</script>

<style scoped>

</style>