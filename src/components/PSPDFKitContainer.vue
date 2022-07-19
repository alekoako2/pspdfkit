<template>
  <div>
    <annotations-sidebar
        hidden

        ref="annotationsSidebar"

        :PSPDFKitInstance="PSPDFKitInstance"
        :annotations="annotations"
        @add-annotation="add_annotation_to_array_handler"

        @delete-annotation="delete_annotation_handler"
    />
    <div id="documentViewer"></div>
  </div>
</template>

<script>
import PSPDFKit from "pspdfkit";
import AnnotationsSidebar from "@/components/AnnotationsSidebar";

/**
 * PSPDFKit for Web example component.
 */
export default {
  name: 'PSPDFKit',
  /**
   * The component receives `pdfFile` as a prop, which is type of `String` and is required.
   */
  props: {
    pdfFile: {
      type: String,
      required: true,
    },
  },
  data: function () {
    return {
      PSPDFKitInstance: null,
      customUI: null,
      MOCK_DATA: [
        {
          id: 1,
          number: 1,
          active: false,
          text: 'Ut nonummy. Fusce aliquet pede non pede. Suspendisse dapibus lorem pellentesque magna. Integer nulla',
          label: 'Limitation of liability',
          initial: false
        }
      ],
      annotations: [],
    }
  },
  /**
   * We wait until the template has been rendered to load the document into the library.
   */
  mounted() {
    this.setup_custom_UI();
    this.load_PSPDFKit();
  },
  /**
   * We watch for `pdfFile` prop changes and trigger unloading and loading when there's a new document to load.
   */
  watch: {
    pdfFile(val) {
      if (val) {
        this.load_PSPDFKit();
      }
    },
  },
  /**
   * Our component has the `loadPSPDFKit` method. This unloads and cleans up the component and triggers document loading.
   */
  methods: {
    setup_custom_UI: function () {
      this.customUI = {
        [PSPDFKit.UIElement.Sidebar]: {
          [PSPDFKit.SidebarMode.CUSTOM]: ({containerNode}) => {
            containerNode.appendChild(this.$refs.annotationsSidebar.$el)
            this.$refs.annotationsSidebar.$el.hidden = false;
            return {
              node: containerNode
            }
          }
        }
      }
    },
    load_PSPDFKit: function () {
      PSPDFKit.unload("#documentViewer");
      PSPDFKit.load({
        container: "#documentViewer",
        //document: "document.pdf"
        document: this.pdfFile,
        customUI: this.customUI,
        initialViewState: new PSPDFKit.ViewState({
          sidebarPlacement: PSPDFKit.SidebarPlacement.END
        }),
        //disableWebAssemblyStreaming: true,
        //extension: "<%=HTMLUtils.encodeForJavaScript(extension)%>",
        /*toolbarItems: defaultConfiguration.toolbarItems.reduce((acc, item) => {
            if (item.type === "polyline") {
                return acc.concat([item, { type: "undo" }, { type: "redo" }]);
            }

            return acc.concat([item]);
        }, []),*/
      })
          .then(this.PSPDFKit_loaded_handler)
          .catch(function (error) {
            console.error(error.message);
          });

      /*WebViewer({
          path: '<design:shortrewrite page="/js/webviewer"/>',
          // licenseKey: 'Insert commercial license key here after purchase',
          initialDoc: "<%=HTMLUtils.encodeForJavaScript(url)%>",
          extension: "<%=HTMLUtils.encodeForJavaScript(extension)%>",
          // initialDoc: '/path/to/my/file.pdf', // You can also use documents on your server
      }, this.$refs.documentViewer)
          .then(this.webViewer_loaded_handler);
     */
    },

    add_annotation_to_array_handler: function (annotation) {
      this.annotations = [...this.annotations.filter((annot) => annot.id !== annotation.id), {
        ...annotation,
        initial: false,
        active: false
      }]
    },
    create_annotation_handler: async function (annotations) {
      const annotation = annotations.get(0);

      this.push_to_annotations(annotation).catch(console.log)

      this.show_annotations_sidebar();

    },
    delete_annotation_handler: function (id) {
      this.annotations = this.annotations.filter((annotation) => annotation.id !== id);
      this.PSPDFKitInstance.delete(id)
    },
    focus_annotation_handler: function ({annotation}) {
      this.annotations.find((annot) => annot.id === annotation.id).active = true;
      this.show_annotations_sidebar();
    },

    PSPDFKit_loaded_handler: function (instance) {
      this.PSPDFKitInstance = instance;
      this.$emit("loaded", instance);
      this.load_initial_annotations();

      // hide the Shapes, Edit and Insert toolbar groups.
      this.add_custom_toolbar_tools();
    },

    add_custom_toolbar_tools: function () {


      const tools = [
        {
          type: "custom",
          title: "Annotations Sidebar",
          id: "al-sidebar-annotations",
          onPress: () => {
            this.show_annotations_sidebar();
          }
        },
        {
          type: "custom",
          title: "File Configuration",
          id: "al-file-configuration",
          onPress: (event) => {
            console.log(event)
          }
        },
        {
          type: "custom",
          title: "139%",
          id: "al-zoom",
          dropdownGroup: "al-zoom",
        },

        {
          type: "custom",
          title: "100%",
          id: "al-zoom-100",
          dropdownGroup: "al-zoom",
        },
        {
          type: "custom",
          title: "Zoom In",
          id: "al-zoom-in",
          onPress: (event) => {
            console.log(event)
          }
        },
        {
          type: "custom",
          title: "Zoom Out",
          id: "al-zoom-out",
          onPress: (event) => {
            console.log(event)
          }
        },
        {
          type: "custom",
          title: "Annotations",
          id: "al-annotations",
          onPress: (event) => {
            console.log(event)
          }
        },
        {
          type: "custom",
          title: "Details",
          id: "al-details",
          onPress: (event) => {
            console.log(event)
          }
        },
        {
          type: "custom",
          title: "Search",
          id: "al-search",
          onPress: (event) => {
            console.log(event)
          }
        },
        {
          type: "custom",
          title: "More",
          id: "al-more",
          dropdownGroup: "al-more-group",
        },
        {
          type: "custom",
          title: "View Details",
          id: "al-view-details",
          dropdownGroup: "al-more-group",
        },
        {
          type: "custom",
          title: "View training progress",
          id: "al-view-training-progress",
          dropdownGroup: "al-more-group",
        },
        {
          type: "custom",
          title: "Copy Link",
          id: "al-copy-link",
          dropdownGroup: "al-more-group",
        },
        {
          type: "custom",
          title: "Email",
          id: "al-email",
          dropdownGroup: "al-more-group",
        },
        {
          type: "custom",
          title: "Print Records",
          id: "al-print-records",
          dropdownGroup: "al-more-group",
        },
        {
          type: "custom",
          title: "Sync annotations to original contract",
          id: "al-sync-annotations-to-original-contract",
          dropdownGroup: "al-more-group",
        },
        {
          type: "custom",
          title: "Download",
          id: "al-download",
          dropdownGroup: "al-more-group",
        },
        {
          type: "custom",
          title: "Delete",
          id: "al-delete",
          dropdownGroup: "al-more-group",
        },
      ]
      this.PSPDFKitInstance.setToolbarItems(tools);
    },
    load_initial_annotations: async function () {
      for (const annotation of this.MOCK_DATA) {
        const results = await this.PSPDFKitInstance.search(annotation.text);

        for (const value of results) {
          const index = results.indexOf(value);
          if (index === annotation.number) {
            const annotationsObj = await this.PSPDFKitInstance.create(new PSPDFKit.Annotations.HighlightAnnotation({
              pageIndex: value.pageIndex,
              rects: value.rectsOnPage,
              boundingBox: PSPDFKit.Geometry.Rect.union(value.rectsOnPage)
            }));

            this.push_to_annotations(annotationsObj[0], {label: annotation.label, initial: false}).catch(console.log)
          }
        }
      }

      this.add_event_listeners();
    },
    search_listener: function (resolve, __, ___, [{Quads, pageNum, resultStr}]) {
      resolve();
      const {Annotations} = this.Core;

      const textToHighlight = {
        Quads: Quads.map(quad => quad.getPoints()),
        pageNum,
        text: resultStr
      };

      const highlight = new Annotations.TextHighlightAnnotation();
      highlight.PageNumber = textToHighlight.pageNum;

      highlight.Quads = textToHighlight.Quads


      this.Core.annotationManager.addAnnotation(highlight);
    },

    // helpers
    reset_search_panel_after_initialization: function () {
      this.UI.removeSearchListener(this.search_listener);
      this.enable_search_panel();
      setTimeout(() => {
        this.clear_and_close_search_panel();
      }, 0);

    },
    clear_and_close_search_panel: function () {
      this.Core.documentViewer.clearSearchResults()
      this.UI.toggleElement('searchPanel')
    },
    show_annotations_sidebar: function () {
      this.show_sidebar(PSPDFKit.SidebarMode.CUSTOM);
    },
    disable_search_panel: function () {
      this.UI.disableElements(['searchPanel']);
    },
    enable_search_panel: function () {
      this.UI.enableElements(['searchPanel']);
    },
    push_to_annotations: async function (annotation, ops) {
      this.annotations.push(
          {
            id: annotation.id,
            text: await this.PSPDFKitInstance.getMarkupAnnotationText(annotation),
            active: true,
            initial: true,
            ...ops
          }
      )
    },
    add_event_listeners: function () {
      this.PSPDFKitInstance.addEventListener('annotations.create', this.create_annotation_handler);
      this.PSPDFKitInstance.addEventListener('annotations.focus', this.focus_annotation_handler);
    },
    remove_line_breaks: function (str) {
      return str.replace(/\n|\r/g, " ");
    },
    show_sidebar: function (mode) {
      this.PSPDFKitInstance.setViewState((viewState) =>
          viewState.set('sidebarMode', mode),
      );
    }
  },

  /**
   * Clean up when the component is unmounted so it's ready to load another document (not needed in this example).
   */
  beforeUnmount() {
    PSPDFKit.unload(".pdf-container");
  },
  components: {
    // eslint-disable-next-line vue/no-unused-components
    AnnotationsSidebar
  }
};
</script>


<style scoped>
#documentViewer {
  height: 100vh;
}
</style>