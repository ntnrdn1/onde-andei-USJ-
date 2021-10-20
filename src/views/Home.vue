<template>
  <ion-page>
   <ion-header>
     <ion-toolbar>
       <ion-title>Por onde andei?</ion-title>
     </ion-toolbar>
   </ion-header>

   <ion-content>
     <div class="container">
       <h2>Coordenadas</h2>
       <h3>{{ latitude }}</h3>
       <h3>{{ longitude }}</h3>
       <ion-button @click="buscaCidade()">Qual a cidade?</ion-button>
       <h2>{{ cidade }}</h2>
     </div>
   </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonButton, IonContent, IonHeader, IonPage, IonTitle, IonToolbar } from '@ionic/vue';
import { defineComponent } from 'vue';
import { Geolocation } from '@capacitor/geolocation';
import { Http } from '@capacitor-community/http';

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton
  },
  data() {
    return { 
      latitude: 0,
      longitude: 0,
      cidade: '',
    }
  },
  ionViewDidEnter() {
    this.printCurrentPosition();
  },
  methods: {
    printCurrentPosition: async function() {
      const coordinates = await Geolocation.getCurrentPosition();
      console.log('Current position:', coordinates);

      this.latitude = coordinates.coords.latitude;
      this.longitude = coordinates.coords.longitude;
    },
    buscaCidade: async function() {
      const ACCESS_KEY = 'd7ee75870ba1e8af3f09c609e8c46286';

      const options = {
      url: `http://api.positionstack.com/v1/reverse?access_key=${ACCESS_KEY}&query=${this.latitude},${this.longitude}`
      };

      const response = await Http.get(options);
      console.log('Resposta recebida', response);

      this.cidade = response.data.data[2].county + ' - ' + response.data.data[2].region
    }
  }
});
</script>

<style scoped>

.container {
  text-align: center;
}

</style>