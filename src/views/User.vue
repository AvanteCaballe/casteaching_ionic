<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>User</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
      </ion-header>
      <div class="ion-padding">
        <ion-card class="ion-padding">
          <ion-avatar v-if="this.user.profile_photo_path">
            <img :src="storage+this.user.profile_photo_path">
          </ion-avatar>
          <ion-avatar v-else>
            <img src="http://via.placeholder.com/80">
          </ion-avatar>
          <ion-card-header>
            <ion-card-title>{{ this.user && this.user.name}}</ion-card-title>
            <ion-card-subtitle v-if="this.user.superadmin">{{this.admin}}</ion-card-subtitle>
            <p v-else>Usuari sense privilegis</p>
          </ion-card-header>
          <ion-card-content>
            {{ this.user && this.user.email}}
            <ion-card-subtitle></ion-card-subtitle>
          </ion-card-content>
          <ion-button @click="refresh()">
            Refresh
          </ion-button>
        </ion-card>
      </div>

    </ion-content>
  </ion-page>
</template>

<script>
import {
  IonButtons,
  IonContent, IonHeader,
  IonMenuButton,
  IonPage, IonTitle, IonToolbar, toastController,
} from "@ionic/vue";
import store from "../store";
export default {
  name: 'User',
  components: {
    IonMenuButton,
    IonContent,
    IonPage,
    IonButtons,
    IonTitle,
    IonToolbar,
    IonHeader
  },
  data () {
    return {
      user: {},
      storage: "https://casteaching.marcavante.codes/storage/"
    }
  },
  async mounted () {
    this.user = await store.get('user')
  },
  methods:{
    async refresh(){
      let token
      let user
      try {
        token = await store.get('token')
        this.casteaching.setToken(token)
        user = await this.casteaching.user()
      }catch (error){
        console.log(error);
        const toast = await toastController
            .create({
              message: "Problemes al refrescar el perfil d'usuari",
              duration: 2000
            })
        return toast.present();
      }

      await store.set('token', token)
      await store.set('user', user)

      this.user = await store.get('user')
    }
  }
}
</script>