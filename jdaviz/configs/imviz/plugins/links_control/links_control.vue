<template>
  <v-card flat tile>
    <v-card>
      <v-card-text>
        <v-container>
          <v-row>
            <v-col>
              <v-radio-group 
                label="Link type"
                hint="Type of linking to be done."
                v-model="link_type"
                persistent-hint
                row>
                <v-radio
                  v-for="item in link_types"
                  :key="item"
                  :label="item"
                  :value="item"
                ></v-radio>
               </v-radio-group>
            </v-col>
          </v-row>
          <v-row v-if="false">
            <v-col>
              <v-switch
                label="Fallback on Pixels"
                hint="If WCS linking fails, fallback to linking by pixels."
                v-model="wcs_use_fallback"
                persistent-hint>
              </v-switch>
            </v-col>
          </v-row>
          <v-row v-if="link_type == 'WCS'">
            <v-col>
              <v-switch
                label="Fast approximation"
                hint="Use fast approximation for image alignment if possible (accurate to <1 pixel)."
                v-model="wcs_use_affine"
                persistent-hint>
              </v-switch>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
      <v-divider></v-divider>
      <v-card-actions>
        <div class="flex-grow-1"></div>
        <v-row
          justify="left"
          align="center"
          no-gutters
        >
        <v-btn color="primary" text @click="do_link">Link</v-btn>
        </v-row>
      </v-card-actions>
    </v-card>
  </v-card>
</template>

<style>

/* addresses https://github.com/pllim/jdaviz/pull/3#issuecomment-926820530 */
div[role=radiogroup] > legend {
  width: 100%;
}

</style>
