<template>
  <div v-if="menuTooltip.show" :style='styles +menuTooltip.styles'>
    <button
        v-for="{id,title} in items"

        :key="id"

        @click="label_click_handler(id)"
    >
      {{ title }}
    </button>
  </div>
</template>

<script>

export default {
  name: "al-menu-tooltip",
  props: {
    items: Array,
    pspdfkitInstance: Object
  },
  data: function () {
    return {
      styles:
          'display: flex;flex-direction: column;width: 100px;margin: 0 auto;',
      mouseUpPositions: {},
      menuTooltip: {
        show: false,
        styles: '',
        selectedText: {
          boundingRects: {},
          pageIndex: null
        }
      },
    }
  },
  mounted() {
    this.pspdfkitInstance.contentDocument.addEventListener('mouseup', (e) => {
      this.set_mouse_positions(e.clientX, e.clientY);
    })

    this.pspdfkitInstance.addEventListener('textSelection.change', async ($e) => {
      if (!$e) {
        this.menuTooltip.show = false;
        return;
      }

      this.menuTooltip = {
        show: true,
        styles: `top:${this.mouseUpPositions.y}px;left:${this.mouseUpPositions.x}px;position:absolute;`,
        selectedText: {
          rectsOnPage: (await $e.getSelectedRectsPerPage()).get(0).rects,
          pageIndex: $e.startPageIndex
        }
      }
    })

  },
  methods: {
    label_click_handler: function (id) {
      this.menuTooltip.show = false
      this.$emit('label-clicked', {id, selectedText: this.menuTooltip.selectedText})
    },

    set_mouse_positions: function (x, y) {
      this.mouseUpPositions = {
        x, y
      }
    }
  }
}
</script>

<style scoped>

</style>