<template>html</template>

<script>
import axios from "axios";

export default {
  name: "ReservationsList",
  components: {},
  props: {
    reservationDate: String,
  },
  data() {
    return {};
  },
  mounted() {
    console.log(this.reservationDate);
    const postData = { firstName: "Fred", lastName: "Flintstone" };
    const headerPost = {
      headers: {
        "Content-Type": "application/x-www-form-urlencoded",
      },
    };
    axios
      .post(
        "https://reset-reservation.herokuapp.com/api/reservation",
        postData,
        headerPost
      )
      .then((response) => {
        console.log(response);
      })
      .catch((error) => {
        console.log(error.response.data.errors);
      });
  },
  methods: {},
};
</script>

<style scoped>
</style>