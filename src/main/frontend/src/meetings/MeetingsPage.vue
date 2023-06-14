<template>
  <div>
    <NewMeetingForm @added="addNewMeeting($event)"></NewMeetingForm>
    <span v-if="meetings.length == 0">Brak zaplanowanych spotkań.</span>
    <h3 v-else>Zaplanowane zajęcia ({{ meetings.length }})</h3>
    <MeetingsList :meetings="meetings"
                  :username="username"
                  @attend="addMeetingParticipant($event)"
                  @unattend="removeMeetingParticipant($event)"
                  @delete="deleteMeeting($event)"></MeetingsList>
  </div>
</template>

<script>
import NewMeetingForm from "./NewMeetingForm";
import MeetingsList from "./MeetingsList";
import axios from "axios";

export default {

  components: {NewMeetingForm, MeetingsList},
  props: {username: String},
  data() {
    return {
      meetings: [],
    };
  },

  async created() {
    try {
      const meetingsResponse = await axios.get('/api/meetings');
      const meetingsWithParticipants = await Promise.all(
          meetingsResponse.data.map(async (meeting) => {
            const participantsResponse = await axios.get(`/api/meetings/${meeting.id}/participants`);
            return { ...meeting, participants: participantsResponse.data };
          }),
      );
      this.meetings = meetingsWithParticipants;
    } catch (error) {
      console.error(error);
    }
  },



  methods: {
    addNewMeeting(meeting) {
      axios.post("/api/meetings",
          {title: meeting.title,
            description: meeting.description,
          } )
          .then((response) => {
            this.meetings.push({
              id: response.data.id,
              title: response.data.title,
              description: response.data.description,
              participants: []});
          })
          .catch(error => {
            this.errorMessage = error.message;
            console.error("There was an error!", error);
          });

    },
    addMeetingParticipant(meeting) {
      axios.post(`/api/meetings/${meeting.id}/participants`, {login: this.username})
          .then(() => {
            meeting.participants.push(this.username);
            })
          .catch(error => {
            this.errorMessage = error.message;
            console.error("There was an error!", error);
          });
      },
    removeMeetingParticipant(meeting) {
      axios.delete(`/api/meetings/${meeting.id}/participants/${this.username}`)
          .then(() => {
            meeting.participants.splice(meeting.participants.indexOf(this.username), 1);
            })
          .catch(error => {
            this.errorMessage = error.message;
            console.error("There was an error!", error);
          });
      },
    deleteMeeting(meeting) {
      axios.delete('/api/meetings/'+meeting.id)
          .then(() => {
            this.meetings.splice(this.meetings.indexOf(meeting), 1);
            })
          .catch(error => {
            this.errorMessage = error.message;
            console.error("There was an error!", error);
          });
      },
  }
}
</script>