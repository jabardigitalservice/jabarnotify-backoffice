<template>
  <div>
    <h3 class="mb-1">{{ event_name }}</h3>
    <v-card class="rounded-b-0">
      <v-card-text>
        <v-row no-gutters>
          <v-col cols="12">
            <v-row>
              <v-col cols="3">
                <label class="text-subtitle-2 grey--text">Sender</label>
                <p class="font-weight-medium mt-2 mb-0">
                  {{ createdBy.username }}
                </p>
              </v-col>
              <v-col cols="4">
                <label class="text-subtitle-2 grey--text">
                  Notif Subject
                </label>
                <p class="font-weight-medium mt-2 mb-0">
                  {{ subject }}
                </p>
              </v-col>
              <v-col cols="3">
                <label class="text-subtitle-2 grey--text">
                  Total Recipient
                </label>
                <p class="font-weight-medium mt-2 mb-0">
                  {{ recipientTotal }}
                </p>
              </v-col>
              <v-col cols="2">
                <label class="text-subtitle-2 grey--text">Status</label>
                <p class="font-weight-medium mt-2 mb-0">
                  <v-chip label small class="ma-0" :color="getChipColor">
                    {{ 'Sent' }}
                  </v-chip>
                </p>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <label class="text-subtitle-2 grey--text">Body</label>
                <p class="font-weight-medium mt-2 mb-0 text-justify">
                  {{ body }}
                </p>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <label class="text-subtitle-2 grey--text">Recipients</label>
                <v-data-table
                  dense
                  :headers="tableHeaders"
                  :items="recipients"
                  item-key="_id"
                  class="elevation-1"
                ></v-data-table>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-card-text>
      <v-divider></v-divider>
    </v-card>
  </div>
</template>

<script>
/* eslint-disable camelcase */
import { getChipColor } from '@/utilities/formater'

export default {
  filters: {
    getChipColor
  },

  props: {
    data: {
      type: Object,
      default: null
    }
  },

  data() {
    return {
      tableHeaders: [
        {
          text: 'Name',
          align: 'start',
          value: 'name'
        },
        { text: 'Phone number', value: 'phoneNumber' },
        { text: 'Email Address', value: 'emailAddress' },
        { text: 'Status', value: 'status' }
      ],
      subject: null,
      body: null,
      recipientTotal: null,
      recipients: [],
      createdBy: {}
    }
  },

  watch: {
    data(val) {
      this.subject = val ? val.subject : null
      this.body = val ? val.body : null
      this.recipientTotal = val ? val.recipientTotal : null
      this.recipients = val ? val.recipients : []
      this.createdBy = val ? val.createdBy : {}
    }
  }
}
</script>
