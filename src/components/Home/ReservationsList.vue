<template>
  <ion-card>
    <ion-card-content>
      <ion-item>
        <ion-label>Selectionnez une horaire</ion-label>
        <ion-select
          ok-text="Valider"
          cancel-text="Fermer"
          v-model="selectedHourReservation"
          interface="action-sheet"
        >
          <ion-select-option
            v-for="reservation in reservationList"
            :key="reservation.hour"
            :value="reservation.hour"
            >{{ reservation.hour }}H {{ reservation.reservations }}/{{
              informations.reservationLimit
            }}
          </ion-select-option>
        </ion-select>
      </ion-item>
      <ion-item>
        <ion-label position="floating">Adresse mail</ion-label>

        <ion-input type="email" v-model="email"></ion-input>
      </ion-item>
      <br />
      <div class="check-zone">
        <ion-toggle @click="cgucheck"></ion-toggle>
        <ion-label>j'ai lu et j'accepte les CGU</ion-label>
      </div>

      <br />
      <ion-button
        class="btn-default"
        shape="round"
        @click.prevent="sendReservation"
        >Réserver
      </ion-button>
    </ion-card-content>
  </ion-card>
</template>

<script>
import axios from "axios";
import moment from "moment";
import {
  IonItem,
  IonSelect,
  IonSelectOption,
  IonLabel,
  IonInput,
  alertController,
  IonToggle,
  IonButton,
  IonCard,
  IonCardContent,
} from "@ionic/vue";
export default {
  name: "ReservationsList",
  components: {
    IonItem,
    IonSelect,
    IonSelectOption,
    IonLabel,
    IonInput,
    IonToggle,
    IonButton,
    IonCard,
    IonCardContent,
  },
  props: {
    informations: Object,
  },
  data() {
    return {
      reservationDateFormat: "",
      reservationList: [],
      selectedHourReservation: "",
      email: "",
      cgu: false,
    };
  },
  mounted() {
    this.reservationDateFormat = moment(
      this.informations.reservationInputDate
    ).format("YYYY-MM-DD");
    this.generateReservationPossible();
  },
  methods: {
    async presentAlertMultipleButtons(message) {
      let messageFormat = "";
      if (message.errors) {
        Object.keys(message.errors).forEach((error) => {
          messageFormat = messageFormat + "<br />" + message.errors[error][0];
        });
      } else if (message.message) {
        messageFormat = message.message;
      } else {
        messageFormat = message;
      }

      const alert = await alertController.create({
        header: "Erreur",
        message: messageFormat,
        buttons: ["D'accord"],
      });
      return alert.present();
    },
    generateReservationPossible() {
      axios
        .get(
          `https://reset-reservation.herokuapp.com/api/reservation/${this.reservationDateFormat}`
        )
        .then((response) => {
          this.informations.reservationInputDate;
          for (
            let index = this.informations.reservationTimetable[0];
            index <= this.informations.reservationTimetable[1];
            index++
          ) {
            let formatReservation = {
              hour: index,
              reservations: 0,
            };

            if (response.data[index]) {
              if (response.data[index] < this.informations.reservationLimit) {
                formatReservation.reservations = response.data[index];
                this.reservationList.push(formatReservation);
              }
            } else {
              this.reservationList.push(formatReservation);
            }
          }
        })
        .catch((error) => {
          this.presentAlertMultipleButtons(error.response.data.errors);
          console.log(error.response.data.errors);
        });
    },
    cgucheck() {
      if (this.cgu === false) {
        return (this.cgu = true);
      } else {
        return (this.cgu = false);
      }
    },
    sendReservation() {
      let postData = {
        email: this.email,
        cgu: this.cgu,
        selected_day: this.reservationDateFormat,
        selected_hour: this.selectedHourReservation,
      };

      if (this.cgu === false) {
        return this.presentAlertMultipleButtons("Veuillez accepter les CGU.");
      }
      axios
        .post(
          "https://reset-reservation.herokuapp.com/api/reservation",
          postData
        )
        .then((response) => {
          const reservationStore = {
            id: Date.now(),
            email: this.email,
            cgu: this.cgu,
            selected_day: this.reservationDateFormat,
            selected_hour: this.selectedHourReservation,
            token: response.data.token,
          };
          if (localStorage.getItem("reservations")) {
            const getReservationsLocal = JSON.parse(
              localStorage.getItem("reservations")
            );
            getReservationsLocal.push(reservationStore);

            localStorage.setItem(
              "reservations",
              JSON.stringify(getReservationsLocal)
            );
          } else {
            localStorage.setItem(
              "reservations",
              JSON.stringify([reservationStore])
            );
          }
          this.$bus.emit("reservationFinish", { activePage: 1 });
        })
        .catch((error) => {
          console.log(error.response.data);
          this.presentAlertMultipleButtons(error.response.data);
        });
    },
  },
};
</script>

<style scoped>
.check-zone {
  display: flex;
  align-items: center;
  margin-left: 20px;
  margin-bottom: 10px;
}
.check-zone > ion-label {
  padding-left: 20px;
  text-align: left;
}
ion-toggle {
  --background-checked: #060040;
  --handle-background-checked: #fffa0a;
}
</style>