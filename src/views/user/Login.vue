<template>
  <v-form v-model="valid">
    <v-container>
      <v-row>
        <v-col cols="2">

          <v-text-field
              v-model="firstname"
              :rules="nameRules"
              :counter="10"
              label="First name"
              required
          ></v-text-field>
          <v-text-field
              v-model="lastname"
              :rules="nameRules"
              :counter="10"
              label="Last name"
              required
          ></v-text-field>
          <v-text-field
              v-model="email"
              :rules="emailRules"
              label="E-mail"
              required
          ></v-text-field>
        </v-col>
      </v-row>
    </v-container>
  </v-form>
</template>

<script>
export default {
  name: "Login",
  data() {
    return {
      valid: false,
      firstname: '',
      lastname: '',
      nameRules: [
        v => !!v || 'Name is required',
        v => v.length <= 10 || 'Name must be less than 10 characters',
      ],
      email: '',
      emailRules: [
        v => !!v || 'E-mail is required',
        v => /.+@.+/.test(v) || 'E-mail must be valid',
      ],
    }
  }
}
</script>

<style scoped>

</style>