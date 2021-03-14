<template>
  <ion-page>
    <ion-tabs>
      <ion-tab-bar slot="bottom">
        <ion-tab-button tab="home" href="/home">
          <ion-icon :icon="homeOutline" />
          <ion-label>Accueil</ion-label>
        </ion-tab-button>

        <ion-tab-button tab="cancel" href="/cancel">
          <ion-icon :icon="listOutline" />
          <ion-label>Vos réservations</ion-label>
        </ion-tab-button>
      </ion-tab-bar>
    </ion-tabs>
  </ion-page>
</template>

<script>
import {
  IonTabBar,
  IonTabButton,
  IonTabs,
  IonLabel,
  IonIcon,
  IonPage,
} from "@ionic/vue";
import { listOutline, homeOutline } from "ionicons/icons";

export default {
  name: "Tabs",
  components: { IonLabel, IonTabs, IonTabBar, IonTabButton, IonIcon, IonPage },
  setup() {
    return {
      listOutline,
      homeOutline,
    };
  },
};
</script>

<style scoped>
ion-tab-bar {
  --background: #1d1752;
}
ion-tab-button {
  --color: #fff;
  --color-selected: #fffa0a;
}
</style>