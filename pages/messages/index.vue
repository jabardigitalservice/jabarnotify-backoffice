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
          <MessagesForm @createdNotification="onCreatedNotification" />
        </v-card-text>
      </v-card>
    </v-dialog>
    <v-layout>
      <MessagesDatatable
        title="Kegiatan"
        router-name="events"
        :allow="permissions"
        @createNewMessage="onCreateNewMessage"
        @optionChanged="onOptionChange"
      />
    </v-layout>
  </div>
</template>

<script>
import { pickBy, identity } from 'lodash'
import { mapGetters } from 'vuex'
import MessagesDatatable from '@/components/messages/Datatable'
import MessagesForm from '@/components/messages/Form'
import { NOTIFICATION_SUCCESS_CREATE } from '@/utilities/constant'

export default {
  middleware: 'auth',
  components: {
    MessagesDatatable,
    MessagesForm
  },

  data() {
    return {
      editing: false
    }
  },

  computed: {
    ...mapGetters('auth', ['permissions']),
    popup: {
      get() {
        return this.editing
      },
      set(value) {
        this.editing = value
      }
    },
    popupColor() {
      return 'blue'
    },
    popupTitle() {
      if (this.editing) {
        return 'Create new message'
      }
      return ''
    }
  },

  watch: {
    '$route.query': {
      handler(value, oldValue) {
        this.$store.dispatch(
          'messages/getRecords',
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
        text: 'Messages'
      }
    ])
  },

  methods: {
    onCreateNewMessage(value) {
      this.index = -1
      this.editing = true
    },
    onCreatedNotification(error) {
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
      title: 'Messages'
    }
  }
}
</script>
