<template>
  <div>
    <form @submit.prevent="login">
      <h1>Login</h1>
      <div>
        <label for="email">Email</label>
        <input type="text" name="email" id="email" v-model="email">
      </div>
      <div>
        <label for="password">Password</label>
        <input type="text" name="password" id="password" v-model="password">
      </div>
      <button type="submit">Login</button>
    </form>
    {{ $auth.state.loggedIn }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: '',
      password: '',
    }
  },
  methods: {
    async login() {
      try {
        const response = await this.$axios.$post('/graphql', {
          query: `
          mutation login($email: String!, $password: String!) {
            login(email: $email, password: $password) {
              token
            }
          }
        `,
          variables: {
            email: this.email,
            password: this.password,
          }
        });

        const token = response.data.login.token;
        await this.$auth.loginWith('local', { token });
        console.log('login successful');
        this.$router.push('/');
      } catch (err) {
        console.log(err.response.data);
      }
    }


  },
}
</script>