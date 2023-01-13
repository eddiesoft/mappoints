<template>
  <v-app dark>
    <v-app-bar style="z-index: 10000" fixed app>
      <v-toolbar-title>Default Layout</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <Nuxt />
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {};
  },
};
</script>
