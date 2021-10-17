<template>
   <v-app>
      <v-app-bar app color="primary" dark>
         <div class="d-flex align-center">
            <v-img
               alt="Logo"
               src="./assets/nasaLogo.png"
               transition="scale-transition"
               width="145"
            />

            <h1>Landsat</h1>
         </div>

         <v-spacer></v-spacer>

         <v-btn href="https://github.com/kv3cruz/landsat" target="_blank" text>
            <span class="mr-2">Repository</span>
            <v-icon>fa-share-square</v-icon>
         </v-btn>
      </v-app-bar>

      <v-main>
         <router-view />
      </v-main>
   </v-app>
</template>

<script>
export default {
   name: 'App',

   data: () => ({
      //
   })
}
</script>
