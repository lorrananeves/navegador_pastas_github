<template>
  <v-app>
    <v-main class="vmain">
      <GithubVuewer/>
    </v-main>
      <v-footer
    dark
    padless
  >
    <v-card
      flat
      tile
      class="black lighten-1 white--text text-center"
    >
    </v-card>
  </v-footer>

  </v-app>
</template>

<script>
import GithubVuewer from './components/GithubVuewer';

export default {
  name: 'App',

  components: {
    GithubVuewer,
  },

  data: () => ({
    //
  }),
};
</script>
<style>
.vbar{
  display: flex;
    position: relative;
    z-index: 0;
    flex-direction: column;
    flex-wrap: wrap;
    align-content: center;
    justify-content: space-evenly
}
.vmain{
  background-color: pink;
}
</style>
