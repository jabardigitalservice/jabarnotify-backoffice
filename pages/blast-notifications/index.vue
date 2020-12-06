<template>
  <div>
    <!-- Form Modal -->
    <v-dialog v-model="popup" scrollable width="850">
      <v-card>
        <v-toolbar :color="popupColor" dark>
          <v-btn large icon @click="popup = false">
            <v-icon>mdi-plus-circle</v-icon>
          </v-btn>
          <v-toolbar-title class="text-xs-center">{{
            popupTitle
          }}</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <BlastNotificationsView v-if="viewing" :data="getCurrent" />
          <BlastNotificationsForm
            v-if="editing"
            @createdNotification="onCreatedNotification"
          />
        </v-card-text>
      </v-card>
    </v-dialog>
    <v-layout>
      <BlastNotificationsDatatable
        title="Kegiatan"
        router-name="events"
        :allow="permissions"
        @createNewMessage="onCreateNewMessage"
        @detailMessage="onDetailMessage"
        @optionChanged="onOptionChange"
      />
    </v-layout>
  </div>
</template>

<script>
import { pickBy, identity } from 'lodash'
import { mapGetters } from 'vuex'
import BlastNotificationsDatatable from '@/components/blast-notifications/Datatable'
import BlastNotificationsForm from '@/components/blast-notifications/Form'
import BlastNotificationsView from '@/components/blast-notifications/View'
import { NOTIFICATION_SUCCESS_CREATE } from '@/utilities/constant'

export default {
  middleware: 'auth',
  components: {
    BlastNotificationsDatatable,
    BlastNotificationsForm,
    BlastNotificationsView
  },

  data() {
    return {
      notifDetail: {},
      editing: false,
      viewing: false
    }
  },

  computed: {
    ...mapGetters('auth', ['permissions']),
    ...mapGetters('blastNotifications', ['getCurrent', 'getLoading']),
    popup: {
      get() {
        return this.editing || this.viewing
      },
      set(value) {
        this.editing = this.viewing = value
      }
    },
    popupColor() {
      return 'blue'
    },
    popupTitle() {
      if (this.editing) {
        return 'Create blast notifications'
      }
      return ''
    }
  },

  watch: {
    '$route.query': {
      handler(value, oldValue) {
        this.$store.dispatch(
          'blastNotifications/getRecords',
          value.keyWords !== oldValue.keyWords || value.page !== oldValue.page
        )
      },
      deep: true
    }
  },

  created() {
    this.$store.dispatch('breadcrumbs/setItems', [
      {
        disabled: true,
        text: 'Blast Notifications'
      }
    ])
  },

  methods: {
    onCreateNewMessage(value) {
      this.index = -1
      this.editing = true
    },
    onDetailMessage(value) {
      this.$store.dispatch('blastNotifications/getCurrent', value)
      this.viewing = true
    },
    async onCreatedNotification(error) {
      this.index = -1
      this.editing = false
      if (!error) {
        this.$toast.show({
          message: NOTIFICATION_SUCCESS_CREATE,
          type: 'success'
        })
      } else {
        this.$toast.show({ message: error, type: 'error' })
      }
      try {
        await this.$store.dispatch('blastNotifications/getList')
      } catch (error) {}
    },
    async getCurrent({ commit, state }, idEvent) {
      commit('SET_LOADING', true)

      try {
        const { data } = await this.$axios.$get(`/rdt/events/${idEvent}`)
        commit('SET_CURRENT', data)
      } catch (e) {
        //
      } finally {
        commit('SET_LOADING', false)
      }
    },
    onOptionChange(value) {
      let query = { ...this.$route.query }
      if (value.page) query.page = value.page
      query.perPage = value.itemsPerPage || null
      query.sortBy = value.sortBy.length > 0 ? value.sortBy[0] : null
      query.sortOrder = value.sortDesc[0] ? 'desc' : 'asc'
      query.status = value.status
      query.keyWords = value.keyWords
      query = pickBy(query, identity)
      this.$router
        .replace({
          query
        })
        .catch(() => {})
    }
  },

  head() {
    return {
      title: 'Blast Notifications'
    }
  }
}
</script>
