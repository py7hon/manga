<template>
  <v-flex xs12 sm6 offset-sm3>
    <v-card>
      <v-card-title>
        <h5>Masuk</h5>
      </v-card-title>
      <v-card-text>
        <v-form>
          <v-text-field label="Username" v-model="username" required></v-text-field>
          <v-text-field name="Password" label="Masukan Password" hint="At least 8 characters" v-model="password" min="8" :append-icon="e1 ? 'visibility' : 'visibility_off'" :append-icon-cb="() => (e1 = !e1)" :type="e1 ? 'password' : 'text'" required counter></v-text-field>
          <v-btn @click="submit">
            submit
          </v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </v-flex>
</template>

<script>
  export default {
    data: () => {
      return {
        username: '',
        password: '',
        e1: true,
        alert: false
      }
    },
    methods: {
      submit: function (e) {
        this.$axios.$post('/auth/login', JSON.stringify({
          username: this.username,
          password: this.password
        }))
          .then((response) => {
            if (response['response'] === null) {
              this.$store.commit('alerts/add', {
                type: 'error',
                text: 'Error Username atau Password salah'
              })
              return
            }
            var data = response['response'][0]
            if (response['success'] !== true) {
              this.$store.commit('alerts/add', {
                type: 'error',
                text: 'Upaya masuk gagal!'
              })
              return
            }
            this.$store.commit('alerts/add', {
              type: 'success',
              text: 'You signed in successfully!'
            })
            this.$cookie.set('token', data['token'], 1)
            this.$cookie.set('user', true, 1)
            this.$cookie.set('username', data['username'], 1)
            this.$store.commit('username', data['username'])
            this.$store.commit('token', data['token'])
            this.$store.commit('user', true)
            this.alert = true
            this.$router.push('/')
          })
      }
    }
  }
</script>
