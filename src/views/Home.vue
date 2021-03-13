<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tab 1</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tab 1</ion-title>
        </ion-toolbar>
      </ion-header>

      <InputDateForm v-if="activePage == 1" />
      <ReservationsList
        :reservationDate="this.reservationDate"
        v-if="activePage == 2"
      />
    </ion-content>
  </ion-page>
</template>

<script>
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
} from "@ionic/vue";
import InputDateForm from "@/components/Home/InputDateForm.vue";
import ReservationsList from "@/components/Home/ReservationsList.vue";

export default {
  name: "Home",
  components: {
    InputDateForm,
    ReservationsList,
    IonHeader,
    IonToolbar,
    IonTitle,
    IonContent,
    IonPage,
  },
  data() {
    return {
      activePage: 1,
      reservationDate: null,
    };
  },
  mounted() {
    this.$bus.on("sendReservationDate", (date) => {
      this.reservationDate = date;
      this.activePage = 2;
    });
  },
};
</script>