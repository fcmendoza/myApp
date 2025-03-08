<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Meditation</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Meditation</ion-title>
        </ion-toolbar>
      </ion-header>
      <p>Hello to today's meditation</p>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent } from '@ionic/vue';
</script>
