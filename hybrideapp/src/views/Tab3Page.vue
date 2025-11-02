<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>Tickets</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tickets</ion-title>
        </ion-toolbar>
      </ion-header>

      <ion-grid>
        <ion-row>
          <ion-col size="12" v-for="ticket in tickets" :key="ticket.visitor_id + '-' + ticket.concert_id">
            <ion-card>
              <ion-card-header>
                <ion-card-title>
                  🎟️ Ticket – {{ getVisitorName(ticket.visitor_id) }} voor {{ getConcertName(ticket.concert_id) }}
                </ion-card-title>
              </ion-card-header>

              <ion-card-content>
                <p><strong>Bezoeker:</strong> {{ getVisitorName(ticket.visitor_id) }}</p>
                <p><strong>Concert:</strong> {{ getConcertName(ticket.concert_id) }}</p>
                <p><strong>Aantal:</strong> {{ ticket.qty }}</p>

                <ion-row class="ion-justify-content-end ion-margin-top">
                  <ion-col size="auto">
                    <ion-button color="warning" @click="openBewerkForm(ticket)">✏️</ion-button>
                  </ion-col>
                  <ion-col size="auto">
                    <ion-button color="danger" @click="verwijderTicket(ticket.visitor_id, ticket.concert_id)">🗑️</ion-button>
                  </ion-col>
                </ion-row>
              </ion-card-content>
            </ion-card>
          </ion-col>
        </ion-row>
      </ion-grid>

      <ion-fab horizontal="end" vertical="bottom" slot="fixed">
        <ion-fab-button color="primary" @click="openNieuwForm">✚</ion-fab-button>
      </ion-fab>

      <ion-popover :is-open="popoverOpen" @didDismiss="closePopover">
        <ion-content class="ion-padding">
          <h2 style="text-align:center;">{{ bewerkModus ? "Ticket bewerken" : "Nieuw ticket" }}</h2>

          <ion-item>
            <ion-select label="Bezoeker" v-model="huidigTicket.visitor_id">
              <ion-select-option v-for="b in bezoekers" :key="b.id" :value="b.id">
                {{ b.first_name }} {{ b.last_name }}
              </ion-select-option>
            </ion-select>
          </ion-item>

          <ion-item>
            <ion-select label="Concert" v-model="huidigTicket.concert_id">
              <ion-select-option v-for="c in concerten" :key="c.co_id" :value="c.co_id">
                {{ c.co_artist }}
              </ion-select-option>
            </ion-select>
          </ion-item>

          <ion-item>
            <ion-input type="number" label="Aantal" v-model="huidigTicket.qty"></ion-input>
          </ion-item>

          <ion-row>
            <ion-col>
              <ion-button expand="block" color="primary" @click="opslaanTicket">Bewaren</ion-button>
            </ion-col>
            <ion-col>
              <ion-button expand="block" color="medium" @click="closePopover">Annuleren</ion-button>
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
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent,
  IonGrid, IonRow, IonCol, IonCard, IonCardHeader,
  IonCardTitle, IonCardContent, IonButton, IonPopover,
  IonItem, IonInput, IonSelect, IonSelectOption, IonFab, IonFabButton
} from "@ionic/vue";

const axios = inject("axios");

// API LINKS
const API_TICKETS = "https://anaseddaouodisee.be/project1.0/tickets.php";
const API_CONCERTEN = "https://anaseddaouodisee.be/project1.0/concerts.php";
const API_BEZOEKERS = "https://anaseddaouodisee.be/project1.0/visitors.php";

// VARIABELEN
const tickets = ref([]);
const concerten = ref([]);
const bezoekers = ref([]);
const popoverOpen = ref(false);
const bewerkModus = ref(false);

const huidigTicket = ref({
  visitor_id: "",
  concert_id: "",
  qty: ""
});

// LADEN
const laadTickets = async () => {
  const res = await axios.get(API_TICKETS);
  tickets.value = res.data.data || [];
};
const laadConcerten = async () => {
  const res = await axios.get(API_CONCERTEN);
  concerten.value = res.data.data || [];
};
const laadBezoekers = async () => {
  const res = await axios.get(API_BEZOEKERS);
  bezoekers.value = res.data.data || [];
};

const opslaanTicket = async () => {
  // Controleer of alles ingevuld is
  if (!huidigTicket.value.visitor_id || !huidigTicket.value.concert_id || !huidigTicket.value.qty) {
    alert("Vul alle velden in!");
    return;
  }

  try {
    if (bewerkModus.value) {
      await axios.put(API_TICKETS, huidigTicket.value);
      alert("Ticket aangepast!");
    } else {
      await axios.post(API_TICKETS, huidigTicket.value);
      alert("Ticket toegevoegd!");
    }
    laadTickets();
    closePopover();
  } catch {
    alert("Er is iets misgegaan bij het opslaan.");
  }
};

const verwijderTicket = async (visitor_id, concert_id) => {
  if (!confirm("Weet je zeker dat je dit ticket wilt verwijderen?")) return;
  await axios.delete(API_TICKETS, { data: { visitor_id, concert_id } });
  alert("Ticket verwijderd!");
  laadTickets();
};

const openNieuwForm = () => {
  resetForm();
  bewerkModus.value = false;
  popoverOpen.value = true;
};
const openBewerkForm = (ticket) => {
  huidigTicket.value = { ...ticket };
  bewerkModus.value = true;
  popoverOpen.value = true;
};
const closePopover = () => {
  popoverOpen.value = false;
  resetForm();
};
const resetForm = () => {
  huidigTicket.value = { visitor_id: "", concert_id: "", qty: "" };
};

const getVisitorName = (id) => {
  const v = bezoekers.value.find((b) => b.id === id);
  return v ? v.first_name + " " + v.last_name : "Onbekend";
};
const getConcertName = (id) => {
  const c = concerten.value.find((x) => x.co_id === id);
  return c ? c.co_artist : "Onbekend";
};

onMounted(() => {
  laadTickets();
  laadConcerten();
  laadBezoekers();
});
</script>
