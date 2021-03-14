<template>
  <ion-page>
    <ion-content :fullscreen="true">
      <h1>Vos réservations</h1>
      <ReservationsLocal :reservations="this.reservations" />
    </ion-content>
  </ion-page>
</template>

<script>
import { IonPage, IonContent } from "@ionic/vue";
import ReservationsLocal from "@/components/Cancel/ReservationsLocal.vue";

export default {
  name: "Cancel",
  components: {
    ReservationsLocal,

    IonContent,
    IonPage,
  },
  data() {
    return {
      reservations: "",
    };
  },
  created() {
    if (localStorage.getItem("reservations")) {
      this.reservations = JSON.parse(localStorage.getItem("reservations"));
    }
  },
  mounted() {
    this.$bus.on("reservationFinish", () => {
      this.reservations = JSON.parse(localStorage.getItem("reservations"));
    });
  },
};
</script>

<style>
ion-content {
  --background: #060040;
}

ion-toolbar {
  --background: #060040 !important;
  --color: #fff;
}
h1 {
  text-align: center;
  color: #fff;
  margin-bottom: 30px;
}
</style>