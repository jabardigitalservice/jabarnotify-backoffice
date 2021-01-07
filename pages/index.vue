<template>
  <v-row>
    <v-col cols="12" md="4">
      <Summary :title="'SMS'" :total="item.sms" />
    </v-col>
    <v-col cols="12" md="4">
      <Summary :title="'WHATSAPP'" :total="item.whatsapp" />
    </v-col>
    <v-col cols="12" md="4">
      <Summary :title="'EMAIL'" :total="item.email" />
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

  computed: {
    item() {
      return this.$store.getters['blastNotifications/getSummary']
    }
  },

  async created() {
    await this.$store.dispatch('blastNotifications/getSummary')
    this.$store.dispatch('breadcrumbs/setItems', [
      {
        disabled: true,
        text: 'Dashboard'
      }
    ])
  },

  head() {
    return {
      title: 'Welcome'
    }
  }
}
</script>
