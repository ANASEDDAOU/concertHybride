<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>About</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content>

      <ion-card>
        <ion-card-header>
          <ion-card-title>👨‍💻 Ontwikkelaar</ion-card-title>
        </ion-card-header>

        <ion-card-content>
          <ion-grid>
            <ion-row class="ion-justify-content-center">
              <ion-col size="auto">
                <ion-avatar>
                  <ion-img src="wmpf.jpg" alt="Profielfoto"></ion-img>
                </ion-avatar>
              </ion-col>
            </ion-row>

            <ion-row class="ion-text-center ion-margin-top">
              <ion-col>
                <ion-text>
                  <h2><strong>Anas Eddaou</strong></h2>
                  <p>Student Toegepaste Informatica aan Odisee Hogeschool.</p>
                  <p>Gepassioneerd door technologie, design en alles wat met apps te maken heeft.</p>
                  <p>Ik hou ervan om ideeën tot leven te brengen via code en creatieve interfaces.</p>
                </ion-text>
              </ion-col>
            </ion-row>
          </ion-grid>
        </ion-card-content>
      </ion-card>

      <ion-card>
        <ion-card-header>
          <ion-card-title> Over ConcertApp</ion-card-title>
        </ion-card-header>

        <ion-card-content>
          <ion-text>
            <p>
              ConcertApp is een hybride mobiele applicatie waarmee bezoekers tickets kunnen kopen
              voor verschillende concerten. De app combineert gebruiksvriendelijke Ionic-componenten
              met een RESTful API voor dynamische data.
            </p>
            <p>
              Gebruikers kunnen concerten bekijken, bezoekers beheren en tickets toevoegen of verwijderen —
              alles in één moderne interface.
            </p>
          </ion-text>
        </ion-card-content>
      </ion-card>




      

    </ion-content>
  </ion-page>
</template>

<script setup>
import {
  IonPage, IonHeader,IonToolbar, IonTitle,IonContent,IonCard,IonCardHeader,IonCardTitle,IonCardContent,IonGrid,
  IonRow,IonCol, IonAvatar, IonImg,
  IonText,
  
} from "@ionic/vue";

</script>