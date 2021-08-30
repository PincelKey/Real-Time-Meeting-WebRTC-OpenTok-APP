<template>
  <section class="d-flex justify-center pa-4">
    <v-btn to="/session" color="primary" nuxt>
      Join meeting
    </v-btn>
    <v-btn color="primary" :loading="isLoadingNewMeeting" outlined class="ml-3" @click="createMeeting">
      New meeting
    </v-btn>
  </section>
</template>

<script>
export default {
  data () {
    return {
      isLoadingNewMeeting: false
    }
  },
  methods: {
    async createMeeting () {
      this.isLoadingNewMeeting = true

      try {
        const response = await this.$axios.$post('https://192.168.0.118:3300/session')

        this.isLoadingNewMeeting = false

        if (response.status) {
          this.$router.push({ path: '/session' })
        } else {
          alert('Error, try again later')
        }
      } catch (error) {
        this.isLoadingNewMeeting = false

        alert('Error, try again later')
      }
    }
  }
}
</script>
