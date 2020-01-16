<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />Calculadora costes de luna
      </div>
    </v-app-bar>

    <v-content class="bg-ogame">
      <div class="background-overlay">
        <router-view></router-view>
      </div>
    </v-content>
  </v-app>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",

  components: {
    HelloWorld
  },

  data: () => ({
    //
  })
};
</script>
<style lang="scss" scoped>
.bg-ogame {
  background: url("/images/ogame-bg.jpg");
  height: 100%;

  /* Center and scale the image nicely */
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.background-overlay {
  background: rgba(48, 51, 107, 0.4);
  height: 100%;
}
</style>
