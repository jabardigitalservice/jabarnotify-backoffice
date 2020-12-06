<template>
  <validation-observer
    v-slot="{ valid, handleSubmit }"
    tag="div"
    class="v-card__text"
  >
    <form
      class="row justify-end"
      @submit.prevent="handleSubmit(isImport ? doImport : doStore)"
    >
      <v-col cols="12">
        <pkbr-input
          v-model="subject"
          label="Subject"
          placeholder="fill message subject"
          rules="required"
        />
      </v-col>
      <v-col cols="12">
        <pkbr-input
          v-model="body"
          label="Message"
          placeholder="fill text message"
          rules="required"
          :type="'text-area'"
        />
      </v-col>
      <v-col cols="12">
        <pkbr-select
          v-model="type"
          :items="methodOptions"
          label="Type"
          placeholder="Choice message method"
          rules="required"
          item-text="name"
          item-value="value"
        />
      </v-col>
      <v-col cols="12">
        <v-checkbox v-model="isImport" :label="`import xls?`"></v-checkbox>
      </v-col>
      <v-col cols="12" v-if="isImport">
        <pkbr-input
          v-model="attachment"
          label="Import xls"
          type="file"
          class="mt-4"
          name="attachment"
          rules="required"
        />
      </v-col>
      <v-col v-else cols="12">
        <v-row v-for="(recipient, i) in recipients" :key="i">
          <v-col cols="6">
            <pkbr-input
              v-model="recipients[i].name"
              label="Name"
              placeholder="fill recipient name"
              rules="required"
            />
          </v-col>
          <v-col cols="6">
            <pkbr-input
              v-model="recipients[i].phoneNumber"
              label="Phone Number"
              placeholder="fill recipient phone number"
              rules="required"
            />
          </v-col>
        </v-row>
      </v-col>
      <v-col v-show="!isImport" cols="12">
        <v-icon :style="'cursor: pointer'" @click="addRecipient()"
          >mdi-plus-circle</v-icon
        >
        <span>Add more recipient</span>
      </v-col>
      <v-layout row wrap>
        <v-flex layout xs2 md2 offset-xs10>
          <v-btn
            :loading="importLoading"
            :disabled="!valid || !recipients.length"
            type="submit"
            color="blue darken-3 white--text"
          >
            Send
          </v-btn>
        </v-flex>
      </v-layout>
    </form>
  </validation-observer>
</template>

<script>
/* eslint-disable camelcase */
// import { mapGetters } from 'vuex'
export default {
  props: {
    formData: {
      type: Object,
      default: null
    },
    formType: {
      type: String,
      default: 'create'
    }
  },

  data() {
    return {
      importLoading: false,
      attachment: null,
      isImport: false,
      subject: null,
      body: null,
      type: null,
      recipients: [
        {
          name: null,
          phoneNumber: null,
          emailAddress: null
        }
      ],
      methodOptions: [
        { name: 'SMS', value: 'sms' },
        { name: 'Whatsapp', value: 'whatsapp' },
        { name: 'Email', value: 'email', disabled: true }
      ]
    }
  },

  computed: {
    loading() {
      return this.$store.getters['blastNotifications/getLoading']
    }
  },

  watch: {
    formData(val) {
      this.subject = val ? val.subject : null
      this.body = val ? val.body : null
      this.type = val ? val.type : null
      this.recipients = val
        ? val.recipients
        : [
            {
              name: null,
              phoneNumber: null,
              emailAddress: null
            }
          ]
    }
  },

  created() {},
  methods: {
    addRecipient() {
      this.recipients.push({
        name: null,
        phoneNumber: null,
        emailAddress: null
      })
    },

    removeRecipient(index) {
      this.recipients.splice(index, 1)
    },

    async doStore() {
      this.importLoading = true
      const payload = {
        subject: this.subject,
        body: this.body,
        type: this.type,
        recipients: this.recipients
      }

      try {
        const res = await this.$store.dispatch(
          'blastNotifications/store',
          payload
        )
        console.log(res)
        this.$emit('createdNotification', null)
      } catch (error) {
        this.$emit('createdNotification', error)
      }
      this.$emit('onStore', payload)
    },

    async doImport() {
      this.importLoading = true
      const formData = new FormData()
      formData.append('subject', this.subject)
      formData.append('body', this.body)
      formData.append('type', this.type)
      formData.append('attachment', this.attachment)
      console.log('ss', formData)
      try {
        await this.$store.dispatch('blastNotifications/import', {
          formData
        })
        this.$emit('createdNotification', null)
      } catch (error) {
        this.$emit('createdNotification', error)
      }
    }
  }
}
</script>
