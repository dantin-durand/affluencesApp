<template>
  <p>Selectionnez une date:</p>
  <ion-item>
    <ion-datetime
      id="dynamicDisabled"
      :min="ActualyDate"
      v-model="reservationInputDate"
      display-format="DD / MM / YYYY"
      :value="ActualyDate"
    ></ion-datetime>
  </ion-item>
  <br />
  <ion-button class="btn-default" shape="round" @click="reservationDateSelected"
    >Rechercher
  </ion-button>
</template>

<script>
import { IonItem, IonDatetime, alertController, IonButton } from "@ionic/vue";
import moment from "moment";
import axios from "axios";

export default {
  name: "InputDateForm",
  components: {
    IonItem,
    IonDatetime,
    IonButton,
  },
  data() {
    return {
      reservationInputDate: moment().format("YYYY-MM-DD"),
      reservationDays: [],
      reservationTimetable: [],
      reservationLimit: "",
      reservationDuration: "",
      description: "",
      name: "",
      ActualyDate: moment().format("YYYY-MM-DD"),
      MaxDate: Number(moment().format("YYYY")) + 1,
    };
  },
  mounted() {
    axios
      .get(`https://reset-reservation.herokuapp.com/api/config`)
      .then((response) => {
        this.reservationDays = response.data.reservation_days;
        this.reservationTimetable = response.data.reservation_timetable;
        this.reservationLimit = response.data.reservation_limit;
        this.reservationDuration = response.data.reservation_duration;
        this.description = response.data.description;
        this.name = response.data.name;
      })
      .catch((error) => {
        console.log(error);
      });
  },
  methods: {
    async presentAlertMultipleButtons(message) {
      const alert = await alertController.create({
        header: "Erreur",
        message: message,
        buttons: ["D'accord"],
      });
      return alert.present();
    },
    reservationDateSelected() {
      const daySelected = moment(this.reservationInputDate).format("dddd");
      const isBookable = this.reservationDays.filter(
        (day) => day == daySelected
      );
      if (isBookable.length > 0) {
        this.$bus.emit("sendReservationDate", {
          reservationInputDate: this.reservationInputDate,
          reservationDays: this.reservationDays,
          reservationTimetable: this.reservationTimetable,
          reservationLimit: this.reservationLimit,
          reservationDuration: this.reservationDuration,
          description: this.description,
          name: this.name,
        });
      } else {
        this.presentAlertMultipleButtons(
          "Veuillez selectionner une date valide."
        );
      }
    },
  },
};
</script>

<style scoped>
ion-item {
  max-width: 200px;
  margin: auto;
  font-family: monospace !important;
  border-radius: 5px;
}

ion-datetime {
  margin: auto;
}

p {
  font-family: monospace;
  color: #fff;
  font-size: 14px;
}
</style>
