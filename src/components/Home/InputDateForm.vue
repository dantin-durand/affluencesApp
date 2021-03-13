<template>
  <div class="container">
    <ion-item>
      <ion-label position="floating">MM/DD/YYYY</ion-label>
      <ion-datetime
        display-format="DD/MM/YYYY"
        :min="ActualyDate"
        value="2002-09-23T15:03:46.789"
        v-model="reservationInputDate"
        @ionChange="reservationDateSelected"
      ></ion-datetime>
    </ion-item>
    <p>{{ reservationDate }}</p>
  </div>
</template>

<script>
import { IonItem, IonLabel, IonDatetime } from "@ionic/vue";
import moment from "moment";
import axios from "axios";

export default {
  name: "InputDateForm",
  components: {
    IonItem,
    IonLabel,
    IonDatetime,
  },
  data() {
    return {
      reservationInputDate: "",
      reservationDays: [],
      ActualyDate: moment().format("YYYY-MM-DD"),
      MaxDate: Number(moment().format("YYYY")) + 1,
    };
  },
  mounted() {
    axios
      .get(`https://reset-reservation.herokuapp.com/api/reservation`)
      .then((response) => {
        this.reservationDays = response.data.reservation_days;
      })
      .catch((error) => {
        console.log(error);
      });
  },
  methods: {
    reservationDateSelected() {
      const daySelected = moment(this.reservationInputDate).format("dddd");
      const isBookable = this.reservationDays.filter(
        (day) => day == daySelected
      );
      if (isBookable.length > 0) {
        this.$bus.emit("sendReservationDate", this.reservationInputDate);
      }
    },
  },
};
</script>

<style scoped>
.container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}
</style>