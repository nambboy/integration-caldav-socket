<!-- @format -->

<template>
  <div>
    <div>
      <vs-table class="border" max-items="7" multiple v-model="contactSelected" pagination :data="contactGoogle">
        <template slot="header">
          <span class="font-bold m-auto">Contacts</span>
        </template>
        <template slot="thead">
          <vs-th> Name </vs-th>
          <vs-th> Email </vs-th>
          <vs-th> Phone </vs-th>
        </template>

        <template slot-scope="{ data }">
          <vs-tr :data="contact" :key="index" v-for="(contact, index) in data" class="cursor-pointer">
            <vs-td :data="contact.name">
              {{ contact.name }}
            </vs-td>
            <vs-td :data="contact.email">
              {{ contact.email }}
            </vs-td>
            <vs-td :data="contact.phone">
              {{ contact.phone }}
            </vs-td>
            <vs-td>
              <vs-button @click="$emit('sendMail', contact.email)" :disabled="!contact.email" color="skyblue" class="text-xs" icon="send"></vs-button>
              <vs-button color="orange" class="mx-2 text-xs" @click="$emit('inviteContact', contact.email)" icon="event_note"></vs-button>
            </vs-td>
          </vs-tr>
        </template>
      </vs-table>
    </div>
    <div>
      <vs-button color="gray" @click="fetchContactGoogle" icon="contact_mail" class="text-xs my-2"> Fetch Contact Google </vs-button>
      <vs-button :disabled="contactSelected.length > 0 && contactSelected.find(contact => !contact.email)" color="skyblue" @click="$emit('sendMail', contactSelected.map(contact => contact.email).join(', '))" icon="send" class="text-xs mx-5 my-2"> Send Mail </vs-button>
      <vs-button color="orange" class="mx-2 text-xs my-2" @click="$emit('inviteContacts', contactSelected.map(contact => contact.email))" icon="event_note">Create Event</vs-button>
    </div>
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
export default {
  data() {
    return {
      contactGoogle: [],
      contactSelected: []
    }
  },
  methods: {
    ...mapActions({
      getAccessToken: 'userManagement/getAccessToken',
      actionGetContactGoogle: 'userManagement/getContactGoogle',
    }),
    ...mapGetters({
      getContact: 'userManagement/getContact',
    }),
    async fetchContactGoogle() {
      const res = await this.getAccessToken({
        service: 'google',
        username: this.$store.state.userDefaults,
      })
      console.log(res.token)
      await this.actionGetContactGoogle({ access_token: res.token })
      this.contactGoogle = this.getContact().google
      console.log(this.contactGoogle)
    },
  },
  created() {
    this.contactGoogle = this.getContact().google || []
  },
}
</script>

<style></style>
