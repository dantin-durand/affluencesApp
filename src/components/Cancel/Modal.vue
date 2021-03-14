<template>
  <ion-header>
    <div id="header-modal" class="ion-text-center title-container-modal">
      <button class="close-modal-btn" @click="closeModal()">
        <ion-icon :icon="chevronBack" />Retour
      </button>
    </div>
  </ion-header>

  <ion-content class="ion-padding">
    <div class="container-reservation">
      <p>Vous allez annuler la réservation suivante:</p>

      <br />
      <h1>Le {{ reservationDate }}</h1>
      <h1>à {{ reservation.selected_hour }}H</h1>
      <br />
      <ion-button
        color="danger"
        expand="block"
        @click.prevent="removeReservation(reservation.token)"
        >Annuler</ion-button
      >
    </div>
  </ion-content>
</template>

<script>
import { IonContent, IonHeader, IonIcon } from "@ionic/vue";
import { man, flag, location, chevronBack } from "ionicons/icons";
import moment from "moment";
import axios from "axios";

export default {
  components: {
    IonContent,
    IonHeader,
    IonIcon,
  },
  name: "Modal",
  props: {
    reservation: Object,
    reservations: Object,
    closeModal: Function,
  },
  data() {
    return {
      reservationDate: moment(this.reservation.selected_day).format(
        "DD/MM/YYYY"
      ),
    };
  },

  setup() {
    return {
      man,
      flag,
      location,
      chevronBack,
    };
  },

  methods: {
    removeReservation(token) {
      const reservationsAfterDelete = this.reservations.filter(
        (reservation) => reservation.token !== token
      );
      let postData = {
        confirm_annulation: 1,
      };
      axios
        .delete(
          "https://reset-reservation.herokuapp.com/api/reservation/annulation/" +
            token,
          { data: postData }
        )
        .then(() => {
          localStorage.setItem(
            "reservations",
            JSON.stringify(reservationsAfterDelete)
          );
          this.$bus.emit("reservationFinish", { activePage: 1 });
          this.closeModal();
        })
        .catch((error) => {
          console.log(error.response);
        });
    },
  },
};
</script>
<style scoped>
.close-modal-btn:active {
  outline: none;
}
.close-modal-btn {
  position: absolute;
  top: 10px;
  left: 10px;
  background-color: transparent;
  font-size: 16px;
  display: flex;
  align-items: center;
  color: #000;
  transition: all 0.4s ease-in-out;
  z-index: 5;
}
.title-container-modal {
  height: 40px;
  color: white;
  background: #fffa0a;
}
p {
  color: #fff;
  font-size: 18px;
}
.container-reservation {
  text-align: center;
  position: absolute;
  top: 50%;
  left: 50%;
  width: 90%;
  transform: translate(-50%, -50%);
}
h1 {
  font-family: monospace;
  color: #fff;
}

.check-zone {
  display: flex;
  align-items: center;
  margin-left: 20px;
  margin-bottom: 10px;
}
.check-zone > ion-label {
  padding-left: 20px;
  text-align: left;
  color: #fff;
}
ion-toggle {
  --background: #d3d3d3;
  --background-checked: #241c6c;
  --handle-background-checked: #fffa0a;
}
</style>