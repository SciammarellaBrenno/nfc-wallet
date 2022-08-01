<template>
  <ion-page>
    
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Back Button</ion-title>
        <ion-buttons slot="end">
          <ion-button color="primary" @click="Add()">Add</ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">    
      <div v-for="(item, index) in lista" :key="index">
        <ion-card style="padding: 10px">
          <ion-card-title>
            {{nfc.bytesToHexString(item.id)}}
          </ion-card-title>          
          <ion-card-content @click="Share(item)">
            {{item}}
          </ion-card-content>
        </ion-card>
      </div>
    </ion-content>

  </ion-page>
</template>

<script lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonButtons, IonButton, IonCard, IonCardHeader, IonCardContent, loadingController } from '@ionic/vue';
import { defineComponent, ref } from 'vue';
import { NFC } from '@ionic-native/nfc'

export default defineComponent({
  name: 'HomePage',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButtons,
    IonButton,
    IonCard,
    IonCardHeader,
    IonCardContent
  },
  setup(){
    
    let nfc = NFC;
    let flags = nfc.FLAG_READER_NFC_A | nfc.FLAG_READER_NFC_B | nfc.FLAG_READER_NFC_F | nfc.FLAG_READER_NFC_V;

    let lista = ref<any[]>([]);

    var loading = ref<any>();

    async function Add(){
      
      loadingController.create({
        message: "Lendo..."
      }).then((overlay) => {
        loading.value = overlay;
        loading.value.present();

        const abc = nfc.readerMode(flags).subscribe(
          tag => {
            lista.value!.push(tag);
            abc.unsubscribe();
            loading.value.dismiss();
          },
          err => {
            alert(JSON.stringify(err));
            abc.unsubscribe();
            loading.value.dismiss();
          }
        )

      })

    }

    function Share(item){

      loadingController.create({
        message: "Compartilhando..."
      }).then((overlay) => {
        loading.value = overlay;
        loading.value.present();

        const abc = nfc.share(JSON.parse(item)).then(
          res => {
            alert("RES: " + res)
          },
          err => {
            alert("ERR: " + err)
          }
        ).finally(() => {
          nfc.unshare();
          loading.value.dismiss();
        })

      })
      
    }
    
    return{
      lista,
      nfc,
      Add,
      Share
    }
  }
});
</script>

<style scoped>
#container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  color: #8c8c8c;
  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
