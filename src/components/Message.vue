<script setup>
import { useMembresStore } from '@/stores/member'
import { defineProps, inject, onMounted, ref, reactive } from 'vue';
const bus = inject('bus');
const session = inject('session');
const router = inject('router');
// const membresStore = inject('membres');
const membresStore = useMembresStore();

const p = defineProps(['message']);
let isDisplay = ref(true);

const messageElement = ref(null);

let state = reactive({
    id: 0,
    misEnAvant: false,
    membre: '',
    date: ''
})
function showDisplay(){
    isDisplay.value =! isDisplay.value;
}

function deleteMessage(channel_id, id){
  if (confirm("Vous voulez bien supprimer le message? ")) {
    api.delete(`channels/${channel_id}/posts/${id}?token=${session.data.token}`,{
            channel_id:p.message.channel_id,
            id:p.message.id
    }).then(response => {
            bus.emit('recharger-messages');     
        });
    }
}
function editMessage(channel_id, id){
    let body = {
        channel_id : p.message.channel_id,
        id : p.message.id,
        message : p.message.message
    }
  api.put(`channels/${channel_id}/posts/${id}?token=${session.data.token}`,{
   body  
  }).then(response => {
        bus.emit('recharger-messages');
        bus.off('fin-recharger-messages');
        isDisplay=true;
      //  router.push({name : 'conversation', params: { id: channel_id}});

   });
}



onMounted(() => {

    state.membre = membresStore.getMembre(p.message.member_id);
    let date = new Date(p.message.created_at);
    state.date = 'le ' + date.toLocaleDateString() + ' à ' + date.toLocaleTimeString();
    state.id = p.message.id;
    bus.on('defiler-message', (mid) => {
        if (mid == state.id) {
            messageElement.value.scrollIntoView();
            state.misEnAvant = true;
            setTimeout(() => state.misEnAvant = false, 3000);
        }
    });
});

</script>

<template>
    <div class="box" :class="{ 'misEnAvant': state.misEnAvant }">
        <article ref="messageElement" class="media">
            <figure class="media-left">
                <p class="image is-64x64">
                    <img src="https://bulma.io/images/placeholders/128x128.png">
                </p>
            </figure>
            <div class="media-content">
                <div v-if="isDisplay" class="content">
                    <p>
                        <strong>{{ state.membre.fullname }}</strong> <small>{{ state.membre.email }}</small> &middot; 
                        <small>{{ state.date }}</small>
                        <br><br>
                        {{ p.message.message }}
                    </p>
                    <footer class="card-footer">
                        <p class="card-footer-item">
                        <span>
                            <button @click="showDisplay()" class="button is-info ml-4">Modifier</button>
                        </span>
                        </p>
                        <p class="card-footer-item">
                        <span>
                            <button @click="deleteMessage(p.message.channel_id, p.message.id)" class="button is-danger ml-4">Delete</button>
                        </span>
                        </p>
                    </footer>
                </div>
                <form v-else class="container" @submit.prevent="editMessage(p.message.channel_id, p.message.id)">
                    <p>
                        <strong>{{ state.membre.fullname }}</strong> <small>{{ state.membre.email }}</small> &middot; 
                        <small>{{ state.date }}</small>
                    </p>
                    <input class="input is-info mt-4" type="text" placeholder="Ecrivez votre message" v-model="p.message.message">

                    <footer class="card-footer mt-4">
                        <p class="card-footer-item">
                        <span>
                            <button type="submit" class="button is-info ml-4">Modifier</button>
                        </span>
                        </p>
                        <p class="card-footer-item">
                        <span>
                            <button @click="showDisplay()" class="button is-danger is-light ml-4">Annuler</button>
                        </span>
                        </p>
                    </footer>
                </form>
                <nav class="level is-mobile">
                    <div class="level-left">
                        <a class="level-item">
                            <span class="icon is-small"><i class="fas fa-reply"></i></span>
                        </a>
                        <a class="level-item">
                            <span class="icon is-small"><i class="fas fa-retweet"></i></span>
                        </a>
                        <a class="level-item">
                            <span class="icon is-small"><i class="fas fa-heart"></i></span>
                        </a>
                    </div>
                </nav>
            </div>
        </article>
    </div>
</template>

<style scoped>
.box {
    transition: background .5s ease;
}

.misEnAvant {
    background-color: rgb(174, 223, 230);
}
</style>