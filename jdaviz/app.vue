<template>
  <v-app id="web-app" class="jdaviz">
    <v-app-bar color="primary" dark :dense="state.settings.dense_toolbar" flat app absolute clipped-right>
      <v-toolbar-items v-for="item in state.tool_items">
        <v-divider v-if="['g-data-tools', 'g-subset-tools'].indexOf(item.name) === -1" vertical style="margin: 0px 10px"></v-divider>
        <v-divider v-else-if="item.name === 'g-subset-tools'" vertical style="margin: 0px 10px; border-width: 0"></v-divider>
        <j-tooltip :tipid="item.name">
          <jupyter-widget :widget="item.widget" :key="item.name"></jupyter-widget>
        </j-tooltip>
      </v-toolbar-items>
      <v-toolbar-items>
        <v-divider v-if="config === 'mosviz'" vertical style="margin: 0px 10px"></v-divider>
        <j-tooltip v-if="config === 'mosviz'" tipid="lock-row-toggle">
          <v-btn 
            icon
            @click="state.settings.freeze_states_on_row_change = !state.settings.freeze_states_on_row_change"
            :class="{active: state.settings.freeze_states_on_row_change}">
            <v-icon>mdi-vector-link</v-icon>
          </v-btn>
        </j-tooltip>
      </v-toolbar-items>
      <v-spacer></v-spacer>
        <j-tooltip tipid="app-toolbar-plugins">
          <v-toolbar-items>
          <v-btn icon @click="state.drawer = !state.drawer" :class="{active : state.drawer}">
            <v-icon>mdi-menu</v-icon>
          </v-btn>
        </v-toolbar-items>
        </j-tooltip>
    </v-app-bar>

    <v-content
      :style="checkNotebookContext() ? 'height: ' + state.settings.context.notebook.max_height + '; border: solid 1px #e5e5e5;' : 'max-height: calc(100% - 48px)'"
    >
      <v-container class="fill-height pa-0" fluid>
        <splitpanes @resize="relayout">
          <pane size="75">
            <golden-layout
              style="height: 100%;"
              :has-headers="state.settings.visible.tab_headers"
            >
              <gl-row :closable="false">
                <g-viewer-tab
                  v-for="(stack, index) in state.stack_items"
                  :stack="stack"
                  :key="index"
                  :data-items="state.data_items"
                  @resize="relayout"
                  @destroy="destroy_viewer_item($event)"
                  @data-item-selected="data_item_selected($event)"
                  @save-figure="save_figure($event)"
                ></g-viewer-tab>
              </gl-row>
            </golden-layout>
          </pane>
          <pane size="25" v-if="state.drawer" style="background-color: #fafbfc;">
            <v-card flat tile class="overflow-y-auto fill-height" color="#f8f8f8">
              <v-expansion-panels accordion multiple focusable flat tile>
                <v-expansion-panel v-for="(tray, index) in state.tray_items" :key="index">
                  <v-expansion-panel-header>
                    <j-tooltip :tipid="tray.name">
                      {{ tray.label }}
                    </j-tooltip>
                  </v-expansion-panel-header>
                  <v-expansion-panel-content>
                    <jupyter-widget :widget="tray.widget"></jupyter-widget>
                  </v-expansion-panel-content>
                </v-expansion-panel>
              </v-expansion-panels>
              <v-divider></v-divider>
            </v-card>
          </pane>
        </splitpanes>
      </v-container>
    </v-content>
    <v-snackbar
      v-model="state.snackbar.show"
      :timeout="state.snackbar.timeout"
      :color="state.snackbar.color"
      :top=true
      absolute
    >
      {{ state.snackbar.text }}

      <v-progress-circular
              v-if="state.snackbar.loading"
              indeterminate
      ></v-progress-circular>

      <v-btn
              v-if="!state.snackbar.loading"
              text
              @click="close_snackbar_message($event)"
      >
        Close
      </v-btn>
    </v-snackbar>
    <v-fade-transition>
      <div v-show="loading" class="jd-loading-overlay"></div>
    </v-fade-transition>
  </v-app>
</template>

<script>
export default {
  methods: {
    checkNotebookContext() {
      this.notebook_context = document.getElementById("ipython-main-app")
        || document.querySelector('.jp-LabShell');
      return this.notebook_context;
    }
  },
  created() {
    this.$vuetify.theme.themes.light = {
      primary: "#00617E",
      secondary: "#007DA4",
      accent: "#C75109",
      error: '#FF5252',
      info: '#2196F3',
      success: '#4CAF50',
      warning: '#FFC107',
    };
    this.$vuetify.theme.themes.dark = {
      primary: "#00617E",
      secondary: "#007DA4",
      accent: "#C75109",
      error: '#FF5252',
      info: '#2196F3',
      success: '#4CAF50',
      warning: '#FFC107',
    };
  }
};
</script>

<style id="web-app">

/* fix for loading overlay z-index */
div.output_wrapper {
  z-index: auto;
}

.jd-loading-overlay {
  position: absolute;
  inset: 0;
  background-color: white;
  z-index: 100;
  opacity: 0.5;
}

.v-toolbar__content,
.vuetify-styles .v-toolbar__content {
  padding: 0px;
}

.glue__subset-select {
  display: flex;
  align-items: center;
}

.v-tabs-items {
  height: 100%;
}

.splitpanes {
  background-color: #f8f8f8;
}

.splitpanes__splitter {
  background-color: #e2e4e8;
  position: relative;
  width: 5px;
}

.lm_goldenlayout {
  background: #f8f8f8;
}

.lm_content {
  background: #ffffff;
  border: none;
  /*border-top: 1px solid #cccccc;*/
}

.lm_splitter {
  background: #e2e4e8;
  opacity: 1;
  z-index: 1;
}

/* .lm_splitter.lm_vertical {
  height: 1px !important;
}

.lm_splitter.lm_horizontal {
  width: 1px !important;
} */

.lm_header .lm_tab {
  padding-top: 0px;
  margin-top: 0px;
}

.vuetify-styles .lm_header ul {
  padding-left: 0;
}

.v-expansion-panel-content__wrap {
  padding: 0px;
  margin: 0px;
}

.v-expansion-panel__header {
  padding: 0px;
  margin: 0px;
}

.vuetify-styles .v-expansion-panel-content__wrap {
  padding: 0px;
  margin: 0px;
}

.vuetify-styles .v-toolbar__items>span>.v-btn {
  /* allow voolbar-items styling to pass through tooltip wrapping span */
  /* css is copied from .vuetify-styles .v-toolbar__items>.v-btn */
  border-radius: 0;
  height: 100%!important;
  max-height: none;
}

.v-tooltip__content {
  background-color: white !important;
  border-radius: 2px !important;
  border: 1px #003B4D solid !important;
  color: black !important;
}

a:link {
  text-decoration: none;
}

a:visited {
  text-decoration: none;
}

a:hover {
  text-decoration: none;
}

a:active {
  text-decoration: none;
}

.active {
  background-color: #c75109 !important;
}


.v-divider.theme--dark {
  /* make the v-divider standout more */
  border-color: hsla(0,0%,100%,.35) !important;
}

.no-hint .v-text-field__details {
  display: none !important;
}
</style>
