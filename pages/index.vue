<template>
  <v-flex>
    <v-card>
      <v-card-title> Welcome to Jabarnotify Backoffice :) </v-card-title>
    </v-card>
  </v-flex>
</template>

<script>
export default {
  middleware: 'auth',

  components: {
    //
  },

  created() {
    this.$store.dispatch('breadcrumbs/setItems', [])
  },

  head() {
    return {
      title: 'Welcome'
    }
  }
}
</script>
