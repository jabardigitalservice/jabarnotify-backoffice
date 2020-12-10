/* eslint-disable vue/valid-v-bind */
<template>
  <v-flex xs12 md12>
    <v-data-table
      class="v-card v-sheet pkbr-table sticky-last font-weight-light"
      :headers="headers"
      :items="records"
      :_server-items-length="totalItems"
      :options.sync="options"
      :loading="loading"
      fixed-header
      :header-props="{
        class: 'blue-grey lighten-3'
      }"
      :footer-props="{
        'items-per-page-options': [50, 100, 500]
      }"
    >
      <template slot="top">
        <div class="d-flex">
          <v-spacer></v-spacer>
          <v-col cols="auto">
            <v-btn
              small
              title="Create Blast Notifications"
              fab
              dark
              @click="createNewMessage()"
              color="dark"
            >
              <v-icon dark>mdi-plus-circle</v-icon>
            </v-btn>
          </v-col>
        </div>
      </template>
      <template v-slot:[`item.num`]="{ item }">
        {{ item._num }}
      </template>
      <template v-slot:[`item.recipients`]="{ item }">
        <v-layout justify-start>
          <template v-if="['sms', 'whatsapp'].includes(item.type)">
            {{ item.phoneNumber || '-' }}
          </template>
          <template v-else> {{ item.emailAddress || '-' }} </template>
        </v-layout>
      </template>
      <template v-slot:[`item.createdAt`]="{ item }">
        <v-layout justify-start>
          {{
            item.createdAt
              ? $dateFns.format(new Date(item.createdAt), 'dd MMMM yyyy HH:mm')
              : '-'
          }}
        </v-layout>
      </template>
      <template v-show="false" v-slot:[`item.actions`]="{ item }">
        <v-icon class="mr-2" @click="detailMessage(item._id)"> mdi-eye </v-icon>
        <v-icon
          v-if="allow.includes('edit-events')"
          class="mr-2"
          @click="$router.push(`messages/${item.id}/edit`)"
        >
          mdi-pencil
        </v-icon>
        <v-icon
          v-if="allow.includes('delete-events')"
          @click="selectToRemove({ id: item.id, name: item.event_name })"
        >
          mdi-delete
        </v-icon>
      </template>
    </v-data-table>
    <v-dialog v-model="deleteModal" max-width="528">
      <v-card class="text-center">
        <v-card-title>
          <span class="col pl-10">Hapus Kegiatan</span>
        </v-card-title>
        <v-card-text>
          <div>
            {{ confirmDeleteMsg }}
          </div>
          <strong> {{ selectedEvent.name }} </strong>.
        </v-card-text>
        <v-card-actions class="pb-6 justify-center">
          <v-btn
            color="grey darken-1"
            outlined
            class="mr-2 px-2"
            @click="deleteModal = false"
          >
            Tidak
          </v-btn>
          <v-btn
            color="error"
            class="ml-2 px-2"
            @click="remove(selectedEvent.id)"
          >
            Ya
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-flex>
</template>

<script>
import { isEqual } from 'lodash'
import {
  SUCCESS_DELETE,
  FAILED_DELETE,
  CONFIRM_DELETE
} from '@/utilities/constant'
import { getChipColor } from '@/utilities/formater'

const headers = [
  { text: 'No', value: '_num' },
  { text: 'Notify Subject', value: 'subject' },
  { text: 'Notify Type', value: 'type' },
  { text: 'Total Recipients', value: 'recipientTotal', align: 'center' },
  { text: 'Created by', value: 'createdBy.username' },
  { text: 'Created at', value: 'createdAt' },
  { text: 'Actions', value: 'actions', sortable: false, width: 150 }
]

export default {
  filters: {
    getChipColor
  },

  props: {
    title: {
      type: String,
      default: 'Kegiatan'
    },

    allow: {
      type: Array,
      default: () => []
    }
  },

  data() {
    return {
      headers,
      selectedEvent: {
        id: null,
        name: null
      },
      deleteModal: false
    }
  },

  computed: {
    confirmDeleteMsg() {
      return CONFIRM_DELETE + this.selectedEvent.id
    },
    records() {
      return this.$store.getters['blastNotifications/getList']
    },
    pagination() {
      return this.$store.getters['blastNotifications/getPagination']
    },
    loading() {
      return this.$store.getters['blastNotifications/getLoading']
    },
    options: {
      set(value) {
        this.$store.commit('blastNotifications/SET_TABLE_OPTIONS', value)
      },
      get() {
        return this.$store.getters['blastNotifications/getTableOption']
      }
    },
    totalItems() {
      return this.$store.getters['blastNotifications/getTotalData']
    },
    stat: {
      async set(value) {
        await this.$store.dispatch('blastNotifications/resetOptions')
        this.options = {
          ...this.options,
          keyWords: this.searchKey,
          status: value
        }
      },
      get() {
        return this.$route.query.status
      }
    },
    searchKey: {
      async set(value) {
        await this.$store.dispatch('blastNotifications/resetOptions')
        this.options = {
          ...this.options,
          status: this.stat,
          keyWords: value
        }
      },
      get() {
        return this.$route.query.keyWords
      }
    }
  },

  watch: {
    options(value, oldValue) {
      if (!isEqual(oldValue, value)) {
        this.$emit('optionChanged', value)
      }
    }
  },

  mounted() {
    const options = { ...this.options }
    if (this.$route.query.page) {
      options.page = parseInt(this.$route.query.page)
    }
    if (this.$route.query.perPage) {
      options.perPage = parseInt(this.$route.query.perPage)
    }
    if (this.$route.query.sortBy) {
      options.sortBy = [this.$route.query.sortBy]
    }
    if (this.$route.query.sortOrder) {
      options.sortDesc = [this.$route.query.sortOrder === 'desc']
    }
    if (this.$route.query.keyWords) {
      options.keyWords = this.$route.query.keyWords
    }
    if (this.$route.query.status) {
      options.status = this.$route.query.status
    }
    this.options = options
    this.$emit('optionChanged', options)
  },

  methods: {
    createNewMessage() {
      this.$emit('createNewMessage', 'tes')
    },
    detailMessage(id) {
      this.$emit('detailMessage', id)
    },
    formatTanggal(startTanggal, endTanggal) {
      const start = this.$dateFns.format(new Date(startTanggal), 'dd MMM yyyy')
      const end = this.$dateFns.format(new Date(endTanggal), 'dd MMM yyyy')
      const [startDate, startMonth, startYear] = start.split(' ')
      const [endDate, endMonth, endYear] = end.split(' ')
      if (start === end) {
        return start
      } else if (startMonth === endMonth && startYear === endYear) {
        return `${startDate} - ${endDate} ${startMonth} ${startYear}`
      } else if (startMonth !== endMonth && startYear === endYear) {
        return `${startDate} ${startMonth} - ${endDate} ${endMonth} ${startYear}`
      }
      return `${start} - ${end}`
    },
    selectToRemove(payload) {
      this.selectedEvent = payload
      this.deleteModal = true
    },
    async remove(id) {
      try {
        await this.$store.dispatch('blastNotifications/delete', id)
        this.$toast.show({
          message: SUCCESS_DELETE,
          type: 'success'
        })
        await this.$store.dispatch('blastNotifications/getList')
        this.deleteModal = false
      } catch (error) {
        this.$toast.show({
          message: error.message || FAILED_DELETE,
          type: 'error'
        })
      }
    }
  }
}
</script>
