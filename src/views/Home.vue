<template>
  <ion-page>
   <ion-header>
     <ion-toolbar>
       <ion-title>Por onde andei?</ion-title>
     </ion-toolbar>
   </ion-header>

   <ion-content>
     <div class="container">
       <ion-grid>
         <ion-row>
           <ion-col>
                <ion-label>Onde estou?</ion-label>
                <h3>{{ latitude }}</h3>
                <h3>{{ longitude }}</h3>
                <ion-button @click="buscaCidade()">Adicionar Cidade Atual</ion-button>
                <h2>{{ cidade }}</h2>
       </ion-col>
       </ion-row>

       <ion-row>
         <ion-col>
           <ion-input type="text" id="digitaLatitude" placeholder="Latitude,Longitude..." v-model="coordenadas"></ion-input>
         </ion-col>
       </ion-row>

        <ion-row>
         <ion-col>
           <ion-button @click="addCidade">Adicionar cidade com latitude e longitude</ion-button>
         </ion-col>
       </ion-row>

        <ion-row>
         <ion-col>
           <ul v-for="(cidades, index) in list" :key="index">
             <li>{{ cidades }}</li>
           </ul>
         </ion-col>
       </ion-row>

        <ion-row>
         <ion-col>
           <ul v-for="(cidadeLatLon, index) in list2" :key="index">
             <li>{{ cidadeLatLon }}</li>
           </ul>
         </ion-col>
       </ion-row>

       </ion-grid>
     </div>
   </ion-content>
  </ion-page>
</template>

<script>
import { IonButton, IonContent, IonHeader, IonPage, IonTitle, IonToolbar,IonCol, IonGrid, IonRow, IonLabel, IonInput } from '@ionic/vue';
import { defineComponent } from 'vue';
import { Geolocation } from '@capacitor/geolocation';
import { Http } from '@capacitor-community/http';
import { Storage } from '@ionic/storage';

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton,
    IonCol, 
    IonGrid, 
    IonRow, 
    IonLabel,
    IonInput
  },
  data() {
    return { 
      localStorage: new Storage(),
      latitude: 0,
      longitude: 0,
      cidadeLatLon: '',
      cidades: '',
      coordenadas: '',
      coordenadasLat: '',
      list: [],
      list2: []
    }
  },
  created: async function() {
    await this.localStorage.create();
  },
  ionViewDidEnter() {
    this.printCurrentPosition();
  },
  
  methods: {
    addCidade: async function() {
      const ACCESS_KEY = 'd7ee75870ba1e8af3f09c609e8c46286';
      const options = {
      url: `http://api.positionstack.com/v1/reverse?access_key=${ACCESS_KEY}&query=${this.coordenadas}`
      };

      const response = await Http.get(options);
      console.log('Resposta recebida', response);

      this.cidadeLatLon = response.data.data[0].county + ' - ' + response.data.data[0].region;


      this.localStorage.set('list', JSON.stringify(this.list))
      .then( (value) => {
        console.log('deu boa', value);
        })
        .catch( (error) => {
          console.log(error);
        })

      this.list.push(this.cidadeLatLon);
      this.coordenadas = ''
    },

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

      this.cidade = response.data.data[0].county + ' - ' + response.data.data[0].region;

      this.localStorage.set('list', JSON.stringify(this.list))
      .then( (value) => {
        console.log('deu boa', value);
        })
        .catch( (error) => {
          console.log(error);
        })

      
      this.list.push(this.cidade);
      this.cidade = ''
    

    }
  }
});
</script>

<style scoped>


</style>