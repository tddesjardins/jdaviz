<template>
  <v-card flat tile>
    <v-container>
      <j-docs-link :link="'https://jdaviz.readthedocs.io/en/'+vdocs+'/'+config+'/plugins.html#collapse'">Collapse a spectral cube along one axis</j-docs-link>
      <v-row>
        <v-col class="py-0">
          <v-select
            :items="data_items"
            v-model="selected_data_item"
            label="Data"
            hint="Select the data set to use in collapse."
            persistent-hint
          ></v-select>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-select
            :items="axes"
            v-model="selected_axis"
            label="Axis"
            hint="Select the axis along which the data will be collapsed."
            persistent-hint
          ></v-select>
        </v-col>
        <v-col>
          <v-select
            :items="funcs"
            v-model="selected_func"
            label="Method"
            hint="Select the method to use in the collapse."
            persistent-hint
          ></v-select>
        </v-col>
      </v-row>
    </v-container>
    <v-container v-if="selected_axis == 0">
      <v-row>
        <v-col>
          <v-select
            :items="spectral_subset_items"
            v-model="selected_subset"
            label="Spectral Region"
            hint="Select spectral region to apply the collapse."
            persistent-hint
            @click="list_subsets"
          ></v-select>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field
            label="Lower spectral bound"
            v-model="spectral_min"
            hint="Set lower bound."
            persistent-hint
          >
          </v-text-field>
        </v-col>
        <v-col cols = 2>
          <span>{{ spectral_unit }}</span>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field
            label="Upper spectral bound"
            v-model="spectral_max"
            hint="Set upper bound."
            persistent-hint
          >
          </v-text-field>
        </v-col>
        <v-col cols = 2>
          <span>{{ spectral_unit }}</span>
        </v-col>
      </v-row>
    </v-container>
    <!-- <v-divider></v-divider> -->

    <v-card-actions>
      <div class="flex-grow-1"></div>
      <j-tooltip tipid='plugin-collapse-apply'>
        <v-btn color="accent" text @click="collapse">Apply</v-btn>
      </j-tooltip>
    </v-card-actions>
  </v-card>
</template>
