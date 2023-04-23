<template>
  <div id="app">
    <h1>Witaj w systemie do zapisów na zajęcia</h1>

    <div v-if="authenticatedUsername">
      <UserPanel :username="authenticatedUsername" @logout="logMeOut()"></UserPanel>
      <MeetingsPage :username="authenticatedUsername"></MeetingsPage>
    </div>

    <div v-else>
     <button :class="signingUp ? 'button-outline' : '' "@click="signingUp = false"> Logowanie</button>
     <button :class="!signingUp ? 'button-outline' : '' "@click="signingUp = true"> Rejestracja</button>

      <LoginForm v-if ="!siningUp" @login="(user) => logMeIn(user)"></LoginForm>
      <LoginForm v-else @login="(user) => logMeIn(user)" button-label="Załóż konto"></LoginForm>
    </div>
  </div>
</template>

<script>
import "milligram";
import LoginForm from "./LoginForm";
import UserPanel from "./UserPanel";
import MeetingsPage from "./meetings/MeetingsPage";

export default {
  components: {LoginForm, MeetingsPage, UserPanel},
  data() {
    return {
      signingUp: false,
      authenticatedUsername: '',
    }
  },
  methods: {
    logMeIn(user) {
      this.authenticatedUsername = user.login;
    },
    logMeOut() {
      this.authenticatedUsername = '';
    },
    register(user) {
      axios.post('participants', user)
          .then(response => {
              // udało się
          })
          .catch(response => {
              // nie udało sie
          });
    }
  }
}
</script>

<style>
#app {
  max-width: 1000px;
  margin: 0 auto;
}
</style>
