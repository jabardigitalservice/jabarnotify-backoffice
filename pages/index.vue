<template>
  <v-row>
    <v-col cols="12" md="4">
      <Summary :title="'SMS'" />
    </v-col>
    <v-col cols="12" md="4">
      <Summary :title="'WHATSAPP'" />
    </v-col>
    <v-col cols="12" md="4">
      <Summary :title="'EMAIL'" />
    </v-col>
  </v-row>
</template>

<script>
import Summary from '@/components/dashboard/Summary'
export default {
  middleware: 'auth',

  components: {
    Summary
  },

  data() {
    return {
      title: process.env.appName
    }
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
