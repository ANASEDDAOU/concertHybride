<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>Bezoekers</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" fixed-slot-placement="before">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Bezoekers</ion-title>
        </ion-toolbar>
      </ion-header>

      <ion-grid>
        <ion-row>
          <ion-col size="12" v-for="bezoeker in bezoekers" :key="bezoeker.id">
            <ion-card>
              <ion-card-header>
                <ion-card-title>
                  {{ bezoeker.first_name }} {{ bezoeker.last_name }}
                </ion-card-title>
              </ion-card-header>

              <ion-card-content>
                <p><strong>Geboortedatum:</strong> {{ bezoeker.birth_date }}</p>
                <p><strong>E-mail:</strong> {{ bezoeker.email }}</p>

                <ion-row class="ion-justify-content-end ion-margin-top">
                  <ion-col size="auto">
                    <ion-button color="warning" @click="bewerkBezoeker(bezoeker)">
                      ✏️
                    </ion-button>
                  </ion-col>
                  <ion-col size="auto">
                    <ion-button color="danger" @click="verwijderBezoeker(bezoeker.id)">
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
        <ion-fab-button color="primary" @click="openFormulier">
          ✚
        </ion-fab-button>
      </ion-fab>

      <ion-popover
        :is-open="formulierOpen"
        @didDismiss="sluitFormulier"
        :keep-contents-mounted="true"
      >
        <ion-content class="ion-padding">
          <h2 style="text-align:center;">
            {{ huidigBezoeker.id ? "Bewerk bezoeker" : "Nieuwe bezoeker toevoegen" }}
          </h2>

          <ion-item>
            <ion-input
              label="Voornaam"
              label-placement="stacked"
              v-model="huidigBezoeker.first_name"
              required
            ></ion-input>
          </ion-item>

          <ion-item>
            <ion-input
              label="Achternaam"
              label-placement="stacked"
              v-model="huidigBezoeker.last_name"
              required
            ></ion-input>
          </ion-item>

          <ion-item>
            <ion-input
              label="Geboortedatum"
              label-placement="stacked"
              type="date"
              v-model="huidigBezoeker.birth_date"
              required
            ></ion-input>
          </ion-item>

          <ion-item>
            <ion-input
              label="E-mail"
              label-placement="stacked"
              type="email"
              v-model="huidigBezoeker.email"
              required
            ></ion-input>
          </ion-item>

          <ion-row>
            <ion-col>
              <ion-button expand="block" color="primary" @click="opslaanBezoeker">
                Bewaren
              </ion-button>
            </ion-col>

            <ion-col>
              <ion-button expand="block" color="medium" @click="sluitFormulier">
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
  IonPage, IonHeader,IonToolbar, IonTitle, IonContent,
  IonGrid, IonRow,IonCol, IonCard, IonCardHeader, IonCardTitle,IonCardContent, IonItem,
  IonInput,IonButton,IonPopover,
  IonFab, IonFabButton,
} from "@ionic/vue";

const axios = inject("axios");
const API_URL = "https://anaseddaouodisee.be/project1.0/visitors.php";

const bezoekers = ref([]);
const formulierOpen = ref(false);
const huidigBezoeker = ref({
  id: null,
  first_name: "",
  last_name: "",
  birth_date: "",
  email: "",
});

// 🟢 GET
const laadBezoekers = async () => {
  try {
    const res = await axios.get(API_URL);
    bezoekers.value = res.data.data || [];
  } catch {
    alert("Fout bij het laden van bezoekers");
  }
};

const opslaanBezoeker = async () => {
  try {
    if (huidigBezoeker.value.id) {
      await axios.put(API_URL, huidigBezoeker.value);
      alert("Bezoeker bijgewerkt!");
    } else {
      await axios.post(API_URL, huidigBezoeker.value);
      alert("Bezoeker toegevoegd!");
    }
    sluitFormulier();
    laadBezoekers();
  } catch {
    alert("Fout bij opslaan");
  }
};

const verwijderBezoeker = async (id) => {
  if (!confirm("Weet je zeker dat je deze bezoeker wilt verwijderen?")) return;
  try {
    await axios.delete(API_URL, { data: { id } });
    alert("Bezoeker verwijderd!");
    laadBezoekers();
  } catch {
    alert("Fout bij verwijderen");
  }
};

const bewerkBezoeker = (bezoeker) => {
  huidigBezoeker.value = { ...bezoeker };
  formulierOpen.value = true;
};

const openFormulier = () => {
  resetFormulier();
  formulierOpen.value = true;
};

const resetFormulier = () => {
  huidigBezoeker.value = {
    id: null,
    first_name: "",
    last_name: "",
    birth_date: "",
    email: "",
  };
};

const sluitFormulier = () => {
  formulierOpen.value = false;
  resetFormulier();
};

onMounted(() => {
  laadBezoekers();
});
</script>
