<template>
  <ion-page>
    <ion-content :fullscreen="true">
      <br />
      <br />
      <h1 class="title-name">{{ name }}</h1>
      <br />
      <p class="current-text">{{ description }}</p>
      <br />
      <br />
      <br />
      <br />
      <div class="container">
        <div class="header-form" v-if="activePage == 2">
          <ion-button class="btn-default" shape="round" @click="backPage"
            >Retour
          </ion-button>
          <p class="current-text">{{ this.reservationDateFr }}</p>
        </div>
        <InputDateForm v-if="activePage == 1" />
        <ReservationsList
          :informations="this.informations"
          v-if="activePage == 2"
        />
      </div>
    </ion-content>
  </ion-page>
</template>

<script>
import { IonPage, IonContent, IonButton } from "@ionic/vue";
import axios from "axios";
import moment from "moment";
import InputDateForm from "@/components/Home/InputDateForm.vue";
import ReservationsList from "@/components/Home/ReservationsList.vue";

export default {
  name: "Home",
  components: {
    InputDateForm,
    ReservationsList,
    IonContent,
    IonPage,
    IonButton,
  },
  data() {
    return {
      activePage: 1,
      informations: [],
      reservationDateFr: "",
      name: "",
      description: "",
    };
  },
  methods: {
    backPage() {
      this.activePage = 1;
    },
  },
  mounted() {
    axios
      .get(`https://reset-reservation.herokuapp.com/api/config`)
      .then((response) => {
        this.description = response.data.description;
        this.name = response.data.name;
      })
      .catch((error) => {
        console.log(error);
      });
    this.$bus.on("reservationFinish", (result) => {
      this.$router.push("cancel");
      this.activePage = result.activePage;
    });
    this.$bus.on("sendReservationDate", (result) => {
      this.informations = {
        reservationInputDate: result.reservationInputDate,
        reservationDays: result.reservationDays,
        reservationTimetable: result.reservationTimetable,
        reservationLimit: result.reservationLimit,
        reservationDuration: result.reservationDuration,
      };
      this.reservationDateFr = moment(result.reservationInputDate).format(
        "DD / MM / YYYY"
      );
      this.activePage = 2;
    });
  },
};
</script>


<style >
.container {
  text-align: center;
}
ion-content {
  --background: #060040;
}
.current-text {
  color: #fff;
  font-family: monospace;
  text-align: center;
  margin: 0px 40px;
  font-size: 16px;
}
.btn-default {
  --background: #fffa0a;
  --color: #000;
}
.header-form {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0px 20px;
}
.header-form > p {
  margin: 0px;
  text-align: right;
}
</style>