<template>
  <form @submit.prevent="addNewMeeting()">
    <h3>Dodaj nowe spotkanie</h3>
    <label>Nazwa</label>
    <input type="text" v-model="newMeeting.name">
    <label>Opis</label>
    <textarea v-model="newMeeting.description"></textarea>
    <button>Dodaj</button>
    <span class="error" v-if="error">Spotkanie musi mieć nazwę!</span>
  </form>
</template>

<script>
import response from "core-js/internals/is-forced";
import axios from "axios";

export default {
  data() {
    return {
      newMeeting: {participants: []},
      name: '',
      date: '',
      description: '',
      error: false,
    };
  },
  methods: {
    async addNewMeeting() {
      this.error = false;
      if (this.newMeeting.name) {
        this.$emit('added', this.newMeeting);
        this.newMeeting = {participants: []};
      console.log(response.data);
      } else {
        this.error = true;
      }
    }
  }
}
</script>

<style scoped>
.error {
  color: #F00;
}
</style>
