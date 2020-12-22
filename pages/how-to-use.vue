<template>
  <div>
    <v-card>
      <!-- HERE -->
      <iframe
        id="ifrm"
        src="https://wiki.digitalservice.id/share/156f92de-c712-4530-a770-eca96e7a4f78"
        width="100%"
        height="500"
      ></iframe>
    </v-card>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  middleware: 'auth',
  components: {},

  data() {
    return {
      notifDetail: {}
    }
  },

  computed: {
    ...mapGetters('auth', ['permissions'])
  },

  watch: {},

  created() {
    this.$store.dispatch('breadcrumbs/setItems', [
      {
        disabled: true,
        text: 'How to use'
      }
    ])
  },

  methods: {},

  head() {
    return {
      title: 'How to use'
    }
  }
}
</script>
