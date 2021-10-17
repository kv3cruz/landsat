<template>
   <v-container>
      <!-- Loading dialog -->
      <v-dialog v-model="loading" persistent width="200">
         <v-card color="primary" dark class="pt-2 pb-2">
            <v-card-text style="color: white">
               <v-row
                  class="fill-height"
                  align-content="center"
                  justify="center"
               >
                  <v-col class="text-subtitle-1 text-center" cols="12">
                     Loading image...
                  </v-col>
                  <v-col cols="6">
                     <v-progress-linear
                        color="white"
                        indeterminate
                        rounded
                        height="4"
                     ></v-progress-linear>
                  </v-col>
               </v-row>
            </v-card-text>
         </v-card>
      </v-dialog>

      <!-- Snackbar -->
      <v-snackbar
         v-model="snackbar"
         :color="snackColor"
         :right="true"
         :bottom="true"
         :timeout="4000"
      >
         {{ snackText }}

         <template #action="{ attrs }">
            <v-btn color="white" text v-bind="attrs" @click="snackbar = false">
               Close
            </v-btn>
         </template>
      </v-snackbar>

      <!-- input coordinates -->
      <v-row class="text-center justify-center mt-12 ml-16">
         <v-col cols="3">
            <v-form ref="form" v-model="validForm" lazy-validation>
               <v-text-field
                  v-model="coordinates"
                  :rules="coordinatesRules"
                  prepend-inner-icon="fa-map-marker-alt"
                  label="Coordinates"
                  placeholder="latitude,longitude"
                  filled
                  rounded
                  outlined
                  color="primary"
               ></v-text-field>
            </v-form>
         </v-col>
         <v-col cols="1" class="mt-2">
            <v-btn rounded dark color="primary" @click="apiRequest">
               <v-icon left>fa-search</v-icon>Search
            </v-btn>
         </v-col>
      </v-row>

      <!-- nine tiles -->
      <v-card width="800" class="mx-auto" elevation="0">
         <v-row class="mx-16">
            <v-col
               v-for="(img, index) in imgTiles"
               :key="index"
               cols="4"
               class="pa-1"
            >
               <v-card
                  class="d-flex align-center"
                  elevation="1"
                  color="#3DB2FF"
                  height="200"
               >
                  <v-img :src="img.imgUrl" height="200">
                     <template v-if="img.imgUrl != ''" v-slot:placeholder>
                        <v-row
                           class="fill-height my-0"
                           align="center"
                           justify="center"
                        >
                           <v-progress-circular
                              indeterminate
                              color="primary"
                           ></v-progress-circular>
                        </v-row>
                     </template>
                  </v-img>
               </v-card>
            </v-col>
         </v-row>
      </v-card>
   </v-container>
</template>

<script>
import axios from 'axios'
export default {
   name: 'Landsat',

   data: () => ({
      // data
      coordinates: '29.78,-95.33',
      loading: false,
      validForm: true,
      key: 'm8MKhrZocMUxWw0HrDtinnBzJZogeCPMnZewT01W',
      imgTiles: [],
      dim: 0.07,
      // snackbar
      snackbar: false,
      snackColor: 'primary',
      snackText: '',
      //rules
      coordinatesRules: [(v) => !!v || 'Insert a coordinate']
   }),
   created() {
      // initializing the empty images array
      const userCreator = () => ({
         imgUrl: '',
         lat: 0,
         lon: 0
      })
      this.imgTiles = [...new Array(9)].map(() => userCreator())
   },
   methods: {
      apiRequest() {
         const me = this
         if (this.$refs.form.validate()) {
            me.loading = true
            var [latO, lonO] = me.coordinates.split(',')
            latO = parseFloat(latO)
            lonO = parseFloat(lonO)

            // calculating others coordinates and render images

            me.imgTiles.map((el, index) => {
               switch (index) {
                  case 0:
                     el.lat = latO + me.dim
                     el.lon = lonO - me.dim
                     break
                  case 1:
                     el.lat = latO + me.dim
                     el.lon = lonO
                     break
                  case 2:
                     el.lat = latO + me.dim
                     el.lon = lonO + me.dim
                     break
                  case 3:
                     el.lat = latO
                     el.lon = lonO - me.dim
                     break
                  case 4:
                     el.lat = latO
                     el.lon = lonO
                     break
                  case 5:
                     el.lat = latO
                     el.lon = lonO + me.dim
                     break
                  case 6:
                     el.lat = latO - me.dim
                     el.lon = lonO - me.dim
                     break
                  case 7:
                     el.lat = latO - me.dim
                     el.lon = lonO
                     break
                  case 8:
                     el.lat = latO - me.dim
                     el.lon = lonO + me.dim
                     break
               }
               axios
                  .get(
                     'https://api.nasa.gov/planetary/earth/assets?lon=' +
                        el.lon +
                        '&lat=' +
                        el.lat +
                        '&date=2018-01-01&&dim=' +
                        me.dim +
                        '&api_key=' +
                        me.key
                  )
                  .then((response) => {
                     el.imgUrl = response.data.url
                  })
                  .catch((error) => {
                     console.log(error)
                     me.snackbar = true
                     me.snackText = 'Error loading image'
                  })
            })
            setTimeout(() => {
               me.loading = false
            }, 2500)
         }
      }
   }
}
</script>
