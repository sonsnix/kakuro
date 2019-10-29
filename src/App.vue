<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Kakuro</span>
        <span class="font-weight-light"> Next-level Sudoku</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
    </v-app-bar>
    <v-content>
      <Main />
    </v-content>
  </v-app>
</template>

<script lang="ts">
import Vue from "vue";
import Main from "./components/Main.vue";

export default Vue.extend({
  name: "App",
  components: {
    Main
  },
  data: () => ({
    //
  })
});
</script>