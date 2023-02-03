<script setup>
import Message from '@/components/Message.vue';
import { ref, onMounted, inject } from 'vue';
import { useMembresStore } from '@/stores/member';
import { useRoute } from 'vue-router';


// const membresStore = inject('membres');
const membresStore = useMembresStore();
const route = useRoute();
const session = inject('session');
//console.log(route.params.id);

const membres = membresStore.getMembre(route.params.id);


</script>

<template>

  <div class="container">
    <nav class="breadcrumb" aria-label="breadcrumbs">
      <ul>
        <li><router-link to='/liste-membres'>Membres</router-link></li>
        <li>{{ membres.fullname }}</li>

      </ul>
    </nav>
    <div class="columns">
      <figure class="image is-128x128">
        <img class="is-rounded" src="https://bulma.io/images/placeholders/128x128.png">
      </figure>
      <div class="column ml-4">
        <h2 class="title is-3"><b>NAME : </b>{{ membres.fullname }}</h2>
        <p class="subtitle"><b>Email : </b>{{ membres.email }}</p>
        <p class="subtitle"><b>Créer le : </b>{{ membres.created_at }}</p>
      </div>
    </div>
    <!-- <h1>Message</h1>
    <div class="message">
          <div class="message-body">
              <div class="liste-messages">
              <template v-for="message in state.messages">
                  <message :message="message"></message>
              </template>
          </div>
          </div>
      </div> -->
  </div>
</template>

<style scoped>
.container h1{
  text-align:center;
  font-size: 3em;
  font-weight: bold;
  font-weight: 900;
  text-decoration: underline;
}
</style>