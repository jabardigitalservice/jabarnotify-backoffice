<template>
  <validation-observer
    v-slot="{ valid, handleSubmit }"
    tag="div"
    class="v-card__text"
  >
    <form class="row justify-end" @submit.prevent="handleSubmit(doStore)">
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
      <v-col v-for="(pn, i) in phoneNumber" :key="i" cols="12">
        <pkbr-input
          v-model="phoneNumber[i]"
          label="Recipient"
          placeholder="fill recipient phone number"
          rules="required"
        />
      </v-col>
      <v-col cols="12">
        <v-icon :style="'cursor: pointer'" @click="addRecipient()"
          >mdi-plus-circle</v-icon
        >
        <span>Add more recipient</span>
      </v-col>
      <v-layout row wrap>
        <v-flex layout xs2 md2 offset-xs10>
          <v-btn
            :disabled="!valid || !phoneNumber.length"
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
      subject: null,
      body: null,
      type: null,
      phoneNumber: [null],
      emailAddress: null,
      methodOptions: [
        { name: 'SMS', value: 'sms' },
        { name: 'Whatsapp', value: 'whatsapp' },
        { name: 'Email', value: 'email', disabled: true }
      ]
    }
  },

  computed: {},

  watch: {
    formData(val) {
      this.subject = val ? val.subject : null
      this.body = val ? val.body : null
      this.type = val ? val.type : null
      this.phoneNumber = val ? val.event_name : [null]
      this.emailAddress = val ? val.status : null
    }
  },

  created() {},
  methods: {
    addRecipient() {
      this.phoneNumber.push(null)
    },

    removeRecipient(index) {
      this.phoneNumber.splice(index, 1)
    },

    async doStore() {
      const payload = {
        subject: this.subject,
        body: this.body,
        type: this.type,
        phoneNumber: this.phoneNumber,
        emailAddress: this.emailAddress
      }

      try {
        const res = await this.$store.dispatch('messages/store', payload)
        console.log(res)
        this.$emit('createdNotification', null)
      } catch (error) {
        this.$emit('createdNotification', error)
      }
      this.$emit('onStore', payload)
    }
  }
}
</script>
