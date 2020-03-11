<template>
  <v-app>
    <v-app-bar
      app
      color="purple"
      dark
    >
      <v-toolbar-title>Werewolf Calculator</v-toolbar-title>
    </v-app-bar>

    <v-content>
      <HelloWorld/>
    </v-content>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>
