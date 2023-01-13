<template>
  <v-app dark>
    <v-app-bar style="z-index: 10000" fixed app>
      <v-container>
        <v-row>
          <v-toolbar-title>Introduction Page</v-toolbar-title>
          <v-spacer />
          <v-btn elevation="8" outlined @click="isAuthtDialogOpen = true">
            Начать!
          </v-btn>
        </v-row>
      </v-container>
    </v-app-bar>
    <v-main>
      <Nuxt />
    </v-main>
    <!--  Dialogs -->
    <v-dialog
      style="z-index: 10000"
      v-model="isAuthtDialogOpen"
      persistent
      max-width="600px"
    >
      <auth-window
        :closeDialog="() => (isAuthtDialogOpen = false)"
        :register="register"
        :login="login"
      />
    </v-dialog>
  </v-app>
</template>

<script>
import authWindow from "../components/auth-window.vue";

export default {
  components: { authWindow },

  layout: "client",

  data() {
    return {
      isAuthtDialogOpen: false,
    };
  },

  methods: {
    async register({ name, email, password, isSeller }) {
      // try {
      //   await this.$fire.auth.createUserWithEmailAndPassword(
      //     this.email,
      //     this.password
      //   );
      //   this.$router.push("/aftersignup");
      // } catch (e) {
      //   handleError(e);
      // }
    },

    async login({ email, password }) {
      console.log(aaaa);
    },
  },
};
</script>
