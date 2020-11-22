<template>
  <v-app id="inspire">
    <v-main>
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="12" md="12">
            <h1 class="display-1 font-weight-thin mb-4 text-center">
              JABARNOTIFY BACKOFFICE
            </h1>
            <div class="text-center">
              <v-btn
                @click="login()"
                class="subheading text-center"
                rounded
                color="primary"
                dark
              >
                Click here to login!
              </v-btn>
            </div>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  layout: 'auth',

  mounted() {
    this.$keycloak.init()
  },

  methods: {
    login() {
      this.$keycloak.login({ redirectUri: process.env.keycloakRedirectUri })
    }
  }
}
</script>
