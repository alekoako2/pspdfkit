<template>
  <div>
    <template v-if="PSPDFKitInstance">
      <al-menu-tooltip
          :pspdfkit-instance="PSPDFKitInstance"
          :items="labels"

          @label-clicked="label_clicked_handler"
      />
    </template>

    <div ref="hiddenElements" hidden>
      <al-pages-tool
          ref="pagesTool"

          :pspdfkit-instance="PSPDFKitInstance"
      />

      <al-search-tool
          ref="searchTool"

          :pspdfkit-instance="PSPDFKitInstance"
      />

      <custom-sidebar
          ref="customSidebar"

          :PSPDFKitInstance="PSPDFKitInstance"
          :annotations="annotations"
          :details="details"
          :labels="labels"


          @delete-annotation="delete_annotation_handler"
          @edit-annotation="add_edit_annotation_handler"
          @add-annotation="add_edit_annotation_handler"

          @edit-label="edit_label_handler"

          @edit-details="edit_details_handler"
      />
    </div>
    <div id="documentViewer"></div>
  </div>
</template>

<script>
import PSPDFKit from "pspdfkit";
import CustomSidebar from "@/components/CustomSidebar";
import AlPagesTool from "@/components/AlPagesTool";
import AlSearchTool from "@/components/AlSearchTool";
import AlMenuTooltip from "@/components/al-menu-tooltip";

/**
 * PSPDFKit for Web example component.
 */
export default {
  name: 'PSPDFKit',
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
          labelId: 1,
          initial: false
        }
      ],
      annotations: [],
      customSidebarVisible: false,
      labels: [
        {
          id: 1,
          title: 'Force majeure',
          description: 'Description, guidance, annotation policy goes here. It should give more guidance to annotators as it is the rules by which annotators should follow...',
          projects: [
            'Proj 1',
            'Proj 2'
          ],
          status: 'Trained',
          active: false
        },
        {
          id: 2,
          title: 'Force Force Majeure events?',
          description: '',
          projects: [
            'Proj 1',
            'Proj 2'
          ],
          status: 'Published',
          active: false
        },
        {
          id: 3,
          title: 'Limitation of liability',
          description: '',
          projects: [
            'Proj 1',
            'Proj 2'
          ],
          status: 'Untrained',
          active: false
        }
      ],
      details: [
        {
          id: 1,
          name: "contract_title",
          editable: false,
          label: 'Contract Title',
          value: 'Strategic Alliance Agreement'
        },

        {
          id: 2,
          name: "id",
          editable: false,
          label: 'ID',
          value: '123'
        },

        {
          id: 3,
          name: "ocr_quality",
          editable: false,
          label: 'OCR quality',
          value: 'Excellent'
        },

        {
          id: 4,
          name: "tag",
          editable: true,
          label: 'Tags',
          multiple: true,
          value: ['English'],
          options: [
            'English',
            'Pro-seller',
            'Tag 1',
            'Tag 2',
            'Tag 3',
          ]
        },

        {
          id: 5,
          name: "document_type",
          editable: true,
          label: 'Document Type',
          multiple: true,
          value: ['Strategic Alliance Agreement'],
          options: [
            'Strategic Alliance Agreement',
            'Document Type 1',
            'Document Type 2',
            'Document Type 3 ',
            'Document Type 4',
          ]
        },

        {
          id: 6,
          name: "governing_law",
          editable: true,
          label: 'Governing law',
          value: ['New York'],
          options: [
            'New York',
            'Governing law 1',
            'Governing law 2',
            'Governing law 3 ',
            'Governing law 4',
          ]
        },

        {
          id: 7,
          editable: true,
          name: 'dates',
          align: 'split',
          items: [
            {
              name: "contract_start_date",
              label: 'Contract Start Date',
              value: '2021-06-22',
              inputType: 'date',
            },
            {
              name: "contract_end_date",
              label: 'Contract End Date',
              value: '2022-06-22',
              inputType: 'date',
            }
          ]
        },

        {
          id: 8,
          name: "parties",
          editable: true,
          label: 'Parties',
          items: [
            {
              value: 'Blue Water Vaccines Inc',
            },
            {
              value: 'ST JUDE CHILDRENS RESEARCH HOSPITAL ',
            },
          ]
        },


      ]
    }
  },
  mounted() {
    this.setup_custom_UI();
    this.load_PSPDFKit();
  },
  watch: {
    pdfFile(val) {
      if (val) {
        this.load_PSPDFKit();
      }
    },
  },
  methods: {
    setup_custom_UI: function () {
      this.customUI = {
        [PSPDFKit.UIElement.Sidebar]: {
          [PSPDFKit.SidebarMode.CUSTOM]: ({containerNode}) => {
            containerNode.appendChild(this.$refs.customSidebar.$el)
            this.$refs.hiddenElements.hidden = false;
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
        styleSheets: [
          './al-pspdfkit.css'
        ],
        //document: "document.pdf"
        document: this.pdfFile,
        customUI: this.customUI,
        initialViewState: new PSPDFKit.ViewState({
          sidebarMode: PSPDFKit.SidebarMode.CUSTOM,
          sidebarPlacement: PSPDFKit.SidebarPlacement.END,
          zoom: 1
        }),
        annotationToolbarItems: () => ([]),
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

    // Annotations
    create_annotation_handler: async function (annotations) {
      const annotation = annotations.get(0);

      this.push_to_annotations(annotation).catch(console.log)

      this.show_custom_sidebar();

    },
    delete_annotation_handler: function (id) {
      this.annotations = this.annotations.filter((annotation) => annotation.id !== id);
      this.PSPDFKitInstance.delete(id)
    },
    focus_annotation_handler: function ({annotation}) {
      this.annotations.find((annot) => annot.id === annotation.id).active = true;
      this.show_custom_sidebar();
    },
    add_edit_annotation_handler: function (annotation, opts) {
      this.annotations.splice(
          this.annotations.findIndex((annot) => annot.id === annotation.id),
          1, {...annotation, active: false, ...opts}
      )
    },

    // Labels
    edit_label_handler: function (label, opts) {
      this.labels.splice(
          this.labels.indexOf(label),
          1, {...label, active: false, ...opts}
      );
    },

    // Details
    edit_details_handler: function (inputs) {
      this.details = this.details.map(
          detail => {
            const found = inputs.find(input => input.name === detail.name)
            if (found) {
              return {...found}
            } else {
              return detail
            }
          }
      )

    },

    PSPDFKit_loaded_handler: function (instance) {
      this.PSPDFKitInstance = instance;
      this.$emit("loaded", instance);


      // this.load_initial_annotations();

      // hide the Shapes, Edit and Insert toolbar groups.
      this.add_custom_toolbar_tools();
    },

    label_clicked_handler: async function ({id, selectedText}) {
      const label = this.labels.find((label) => label.id === id);

      await this.create_annotation(selectedText.pageIndex, selectedText.rectsOnPage, label, true, true);
    },

    add_custom_toolbar_tools: function () {
      // eslint-disable-next-line no-unused-vars
      const tools = [
        // Left
        {
          type: "custom",
          id: "al-pages-tool",
          node: this.$refs.pagesTool.$el
        },

        {
          type: "custom",
          title: "100%",
          id: "al-zoom-100",
          dropdownGroup: "al-zoom",
        },
        {
          type: "custom",
          title: "Zoom Out",
          id: "al-zoom-out",
          onPress: () => {
            this.PSPDFKitInstance.setViewState((viewState) => viewState.zoomOut());
          }
        },
        {
          type: "custom",
          title: "Zoom In",
          id: "al-zoom-in",
          onPress: () => {
            this.PSPDFKitInstance.setViewState((viewState) => viewState.zoomIn());
          }
        },
        {
          type: "custom",
          title: "50%",
          id: "al-zoom-50",
          onPress: () => {
            this.PSPDFKitInstance.setViewState((viewState) => viewState.set("zoom", 0.5));
          },
          dropdownGroup: "al-zoom",
        },
        {
          type: "custom",
          title: "75%",
          id: "al-zoom-75",
          onPress: () => {
            this.PSPDFKitInstance.setViewState((viewState) => viewState.set("zoom", 0.75));
          },
          dropdownGroup: "al-zoom",
        },
        {
          type: "custom",
          title: "125%",
          id: "al-zoom-125",
          onPress: () => {
            this.PSPDFKitInstance.setViewState((viewState) => viewState.set("zoom", 1.25));
          },
          dropdownGroup: "al-zoom",
        },
        {
          type: "custom",
          title: "150%",
          id: "al-zoom-150",
          onPress: () => {
            this.PSPDFKitInstance.setViewState((viewState) => viewState.set("zoom", 1.5));
          },
          dropdownGroup: "al-zoom",
        },
        {
          type: "custom",
          title: "175%",
          id: "al-zoom-175",
          onPress: () => {
            this.PSPDFKitInstance.setViewState((viewState) => viewState.set("zoom", 1.75));
          },
          dropdownGroup: "al-zoom",
        },
        {
          type: "custom",
          title: "200%",
          id: "al-zoom-200",
          onPress: () => {
            this.PSPDFKitInstance.setViewState((viewState) => viewState.set("zoom", 2));
          },
          dropdownGroup: "al-zoom",
        },

        {
          type: "custom",
          id: "al-space-between",
          node: document.createElement('div')
        },

        // Right
        {
          type: "custom",
          id: "al-search",
          node: this.$refs.searchTool.$el
        },
        {
          type: "custom",
          title: "Mark reviewed",
          id: "al-mark-reviewed",
        },

        {
          type: "custom",
          title: "More",
          id: "al-more",
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

        // {
        //   type: "custom",
        //   title: "View Details",
        //   id: "al-view-details",
        //   dropdownGroup: "al-more-group",
        // },
        // {
        //   type: "custom",
        //   title: "View training progress",
        //   id: "al-view-training-progress",
        //   dropdownGroup: "al-more-group",
        // },
        // {
        //   type: "custom",
        //   title: "Details",
        //   id: "al-details",
        //   onPress: (event) => {
        //     console.log(event)
        //   }
        // },
        //
        // {
        //   type: "custom",
        //   title: "Annotations Sidebar",
        //   id: "al-sidebar-annotations",
        //   // onPress: () => {
        //   //   if (!this.customSidebarVisible)
        //   //     this.show_custom_sidebar();
        //   //   else
        //   //     this.hide_custom_sidebar();
        //   // }
        // },
        // {
        //   type: "custom",
        //   title: "File Configuration",
        //   id: "al-file-configuration",
        //   onPress: (event) => {
        //     console.log(event)
        //   }
        // },
        // {
        //   type: "custom",
        //   title: "Annotations",
        //   id: "al-annotations",
        //   onPress: () => {
        //     if (!this.customSidebarVisible)
        //       this.show_custom_sidebar();
        //     else
        //       this.hide_custom_sidebar();
        //   }
        //
        // },
      ]
      this.PSPDFKitInstance.setToolbarItems(tools);
      tools.find((tool) => tool.id === 'al-space-between').node.parentElement.style.flex = "1 1 0%";
    },
    load_initial_annotations: async function () {
      for (const annotation of this.MOCK_DATA) {
        const results = await this.PSPDFKitInstance.search(annotation.text);

        for (const value of results) {
          const index = results.indexOf(value);
          if (index === annotation.number) {
            const label = this.labels.find((label) => label.id === annotation.labelId);
            await this.create_annotation(value.pageIndex, value.rectsOnPage, label);
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
    show_custom_sidebar: function () {
      this.customSidebarVisible = true;
      this.show_sidebar(PSPDFKit.SidebarMode.CUSTOM);
    },
    hide_custom_sidebar: function () {
      this.customSidebarVisible = false;
      this.show_sidebar(null);
    },
    create_annotation: async function (pageIndex, rects, label, initial, active) {
      const annotationsObj = await this.PSPDFKitInstance.create(
          new PSPDFKit.Annotations.HighlightAnnotation({
            pageIndex,
            rects,
            boundingBox: PSPDFKit.Geometry.Rect.union(rects)
          }));

      await this.push_to_annotations(annotationsObj[0], {label, initial: initial || false, active: active || false})
    },
    disable_search_panel: function () {
      this.UI.disableElements(['searchPanel']);
    },
    enable_search_panel: function () {
      this.UI.enableElements(['searchPanel']);
    },
    push_to_annotations: async function (annotation, opts) {

      this.annotations.push(
          {
            id: annotation.id,
            text: await this.PSPDFKitInstance.getMarkupAnnotationText(annotation),
            active: true,
            initial: true,
            ...opts
          }
      )
    },
    add_event_listeners: function () {
      // this.PSPDFKitInstance.addEventListener('annotations.create', this.create_annotation_handler);
      this.PSPDFKitInstance.addEventListener('annotations.focus', this.focus_annotation_handler);
    },
    remove_line_breaks: function (str) {
      return str.replace(/\n|\r/g, " ");
    },
    show_sidebar: function (mode) {
      this.PSPDFKitInstance.setViewState((viewState) =>
          viewState.set('sidebarMode', mode),
      );
    },
  },

  beforeUnmount() {
    PSPDFKit.unload(".pdf-container");
  },
  components: {
    AlMenuTooltip,
    AlSearchTool,
    AlPagesTool,
    // eslint-disable-next-line vue/no-unused-components
    CustomSidebar
  }
};
</script>


<style scoped>
#documentViewer {
  height: 100vh;
}
</style>