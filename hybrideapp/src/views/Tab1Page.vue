<template>
  <ion-page >
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>Concerten</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" fixed-slot-placement="before">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Concerten</ion-title>
        </ion-toolbar>
      </ion-header>

      <ion-grid>
        <ion-row>
          <ion-col size="12" v-for="concert in concerten" :key="concert.co_id">
            <ion-card>
              <ion-card-header>
                <ion-card-title>{{ concert.co_artist }}</ion-card-title>
              </ion-card-header>

              <ion-card-content>
                <p><strong>Datum:</strong> {{ concert.co_date }} – {{ concert.co_time }}</p>
                <p><strong>Locatie:</strong> {{ concert.co_venue }}</p>
                <p><strong>Prijs:</strong> €{{ concert.co_price }}</p>

                <ion-row class="ion-justify-content-end ion-margin-top">
                  <ion-col size="auto">
                    <ion-button color="warning" @click="openBewerkForm(concert)">
                      ✏️
                    </ion-button>
                  </ion-col>
                  <ion-col size="auto">
                    <ion-button color="danger" @click="verwijderConcert(concert.co_id)">
                      🗑️
                    </ion-button>
                  </ion-col>
                </ion-row>
              </ion-card-content>
            </ion-card>
          </ion-col>
        </ion-row>
      </ion-grid>
      <ion-fab horizontal="end" vertical="bottom" slot="fixed">
        <ion-fab-button color="primary" @click="openNieuwForm">
          ✚
        </ion-fab-button>
      </ion-fab>
      <ion-popover
        :is-open="popoverOpen"
        @didDismiss="closePopover"
        :keep-contents-mounted="true"
      >
        <ion-content class="ion-padding">
          <h2 style="text-align:center;">
            {{ huidigConcert.co_id ? "Bewerk concert" : "Nieuw concert toevoegen" }}
          </h2>

          <ion-item>
            <ion-input
              label="Artiest"
              label-placement="stacked"
              v-model="huidigConcert.co_artist"
              required
            ></ion-input>
          </ion-item>

          <ion-item>
            <ion-input
              label="Datum"
              label-placement="stacked"
              type="date"
              v-model="huidigConcert.co_date"
              required
            ></ion-input>
          </ion-item>

          <ion-item>
            <ion-input
              label="Tijd"
              label-placement="stacked"
              type="time"
              v-model="huidigConcert.co_time"
              required
            ></ion-input>
          </ion-item>

          <ion-item>
            <ion-input
              label="Locatie"
              label-placement="stacked"
              v-model="huidigConcert.co_venue"
              required
            ></ion-input>
          </ion-item>

          <ion-item>
            <ion-input
              label="Prijs (€)"
              label-placement="stacked"
              type="number"
              v-model="huidigConcert.co_price"
              required
            ></ion-input>
          </ion-item>

          <ion-row>
            <ion-col>
              <ion-button expand="block" color="primary" @click="opslaanConcert">
                Bewaren
              </ion-button>
            </ion-col>

            <ion-col>
              <ion-button expand="block" color="medium" @click="closePopover">
                Annuleren
              </ion-button>
            </ion-col>
          </ion-row>
        </ion-content>
      </ion-popover>
    </ion-content>
  </ion-page>
</template>

<script setup>
import { ref, inject, onMounted } from "vue";
import {
  IonPage,IonHeader, IonToolbar,IonTitle, IonContent,IonGrid,IonRow,IonCol,IonCard,IonCardHeader,
  IonCardTitle,IonCardContent,IonItem,IonInput,IonButton,
  IonPopover,IonFab,
  IonFabButton,
} from "@ionic/vue";

const axios = inject("axios");
const API_URL = "https://anaseddaouodisee.be/project1.0/concerts.php";

const concerten = ref([]);
const popoverOpen = ref(false);
const huidigConcert = ref({
  co_id: null,
  co_artist: "",
  co_date: "",
  co_time: "",
  co_venue: "",
  co_price: "",
});

const laadConcerten = async () => {
  try {
    const res = await axios.get(API_URL);
    concerten.value = res.data.data || [];
  } catch {
    alert("Fout bij het laden van concerten");
  }
};

const opslaanConcert = async () => {
  try {
    if (huidigConcert.value.co_id) {
      await axios.put(API_URL, huidigConcert.value);
      alert("Concert bijgewerkt!");
    } else {
      await axios.post(API_URL, huidigConcert.value);
      alert("Concert toegevoegd!");
    }
    closePopover();
    laadConcerten();
  } catch {
    alert("Fout bij opslaan");
  }
};

const verwijderConcert = async (id) => {
  if (!confirm("Weet je zeker dat je dit concert wilt verwijderen?")) return;
  try {
    await axios.delete(API_URL, { data: { co_id: id } });
    alert("Concert verwijderd!");
    laadConcerten();
  } catch {
    alert("Fout bij verwijderen");
  }
};

const openBewerkForm = (concert) => {
  huidigConcert.value = { ...concert };
  popoverOpen.value = true;
};

const openNieuwForm = () => {
  resetForm();
  popoverOpen.value = true;
};

const resetForm = () => {
  huidigConcert.value = {
    co_id: null,
    co_artist: "",
    co_date: "",
    co_time: "",
    co_venue: "",
    co_price: "",
  };
};

const closePopover = () => {
  popoverOpen.value = false;
  resetForm();
};

onMounted(() => {
  laadConcerten();
});
</script>
