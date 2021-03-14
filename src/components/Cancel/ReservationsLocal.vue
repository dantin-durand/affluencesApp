<template>
  <div v-if="reservations.length > 0">
    <ion-card v-for="reservation in reservations" :key="reservation.id">
      <div class="reservation-list-container">
        <p>
          Le {{ reservation.selected_day }} à {{ reservation.selected_hour }}H
          <br />
          <i>{{ reservation.email }}</i>
        </p>

        <ion-button color="danger" @click.prevent="openModal(reservation)"
          >Annuler</ion-button
        >
      </div>
    </ion-card>
  </div>
  <div v-else>
    <p class="current-text">Aucune réservation</p>
  </div>
</template>

<script>
import { modalController, IonButton, IonCard } from "@ionic/vue";
import Modal from "./Modal";

export default {
  name: "ReservationsLocal",
  components: {
    IonButton,
    IonCard,
  },
  props: {
    reservations: Object,
  },
  methods: {
    async createModal(reservation) {
      this.modal = await modalController.create({
        component: Modal,
        componentProps: {
          reservation: reservation,
          reservations: this.reservations,
          closeModal: () => this.closeModal(),
        },
      });
    },
    async openModal(town) {
      await this.createModal(town);
      this.modal.present();
    },
    closeModal() {
      this.modal.dismiss();
    },
  },
};
</script>

<style scoped>
ion-card {
  padding: 10px;
}
ion-card p {
  font-weight: bold !important;
  padding-left: 10px;
  color: #000 !important;
  text-align: left;
}
ion-card i {
  font-size: 13px;
  font-weight: initial;
}
.reservation-list-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
p {
  font-family: monospace;
  color: #fff;
  font-size: 14px;
  text-align: center;
}
</style>