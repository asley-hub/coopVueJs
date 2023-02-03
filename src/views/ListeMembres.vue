<script setup>
import { ref, onMounted, inject } from 'vue';
import { useMembresStore } from '@/stores/member'
import { useSessionStore } from '@/stores/session'
// const membresStore = inject('membres');
const membresStore = useMembresStore();
const sessions = useSessionStore();
const session = inject('session');

function getMembers() {
      api.get("members").then((response) => {
          membresStore.state.membres = response;
        })
}
function deleteMember(id){
  if(sessions.data.member.id == id){
      alert("tu ne peux pas supprimer ton compte");
  }else{
    if(confirm("Vous voulez effacer l'Utilisateurs")){
      api.delete(`members/${id}?token=${session.data.token}`).then(response => {
            console.log(response);
            this.getMembers();
        });
      }
  }
}

onMounted(()=> {
  getMembers();
})

</script>

<template>
  <main>
    <h2 class="title">Liste des membres</h2>
      <div class="box columns is-vcentered" v-for="membre in membresStore.state.membres">
        <div class="column is-6">
          <h2 class="title ">{{ membre.fullname }}</h2>
          <p class="subtitle ">{{ membre.email }}</p>
        </div>
        <div class="column">
          <button class="button is-primary"><router-link :to="{ name: 'liste-membre', params: { id: membre.id  }}">View</router-link></button>
          <button @click="deleteMember(membre.id)" class="button is-danger ml-4">Delete</button>
        </div>
      </div>
  </main>
</template>
<style scoped>

</style>