<script setup>
import PosterMessage from '@/components/PosterMessage.vue'
import Message from '@/components/Message.vue'
import MemberView from '@/views/MemberView.vue'

import { onMounted, ref, inject, reactive } from 'vue'
import { useRoute } from 'vue-router'

const bus = inject('bus');
const route = useRoute();
const session = inject('session');
const router = inject('router');

bus.on('recharger-messages', chargerMessages);

let state = reactive({
    // les détails de la conversation choisie
    channel: {},
    // les messages postés dans cette conversation
    messages: []
});

let isDisplay = ref(true);
function deleteConversation(id){
    if(confirm("Vous voulez bien effacer toutes les messages et la conversations ??")){
        api.delete(`channels/${id}?token=${session.data.token}`).then(response => {
            router.push('/');
            });
    }
}

function showhide(){
    isDisplay.value = !isDisplay.value;
}

function editConversation(id){
    api.put(`channels/${id}?token=${session.data.token}`,{
        topic: state.channel.topic,
        label: state.channel.label,
        body: state.channel
    }).then(response => {
        router.push("/");
    });
}

async function chargerMessages() {
    const response = await api.get(`channels/${route.params.id}/posts?token=${session.data.token}`);
    state.messages = response.reverse();
    setTimeout(() => bus.emit('fin-recharger-messages'), 10);

}

async function chargerConversation() {
    const response = await api.get(`channels/${route.params.id}?token=${session.data.token}`);
    state.channel = response;

}
onMounted(() => {
    chargerConversation();
    chargerMessages();
});
</script>
<template>        
    <section v-if="state.channel.id" class="hero is-small is-light">
    <div v-if="isDisplay" class="hero-body">
        <h1 class="title">
            {{ state.channel.topic }}
        </h1>
        <p class="subtitle">
           {{ state.channel.label }}
        </p>
        <div class="btn">
            <button @click="showhide()" class="button is-primary">Edit</button>
            <button @click="deleteConversation(state.channel.id)" class="button is-danger">Delete</button>
        </div>
    </div>
    <form v-else class="p-6" @submit.prevent="editConversation(state.channel.id)">
    <div class="field">
        <div class="control has-icons-left has-icons-right">
            <label for="topic">Sujet de la conversation</label>
            <input class="input is-primary" type="text" placeholder="Sujet de la conversation" v-model="state.channel.topic">
        </div>
    </div>
    <div class="field" >
        <div class="control has-icons-left has-icons-right">
            <label for="topic">Description</label>
            <input class="input is-primary" type="text" placeholder="Description" v-model="state.channel.label" >
        </div>
    </div>
    <div class="field">
    <p class="control">
        <button class="button is-primary" type="submit">MODIFIER LA CONVERSATION</button>
        <button @click="showhide()" class="button is-danger ml-2">ANNULER</button>
    </p>
    </div>
    </form>
    <div class="message">
        <div class="message-body">
            <div class="liste-messages">
            <template v-for="message in state.messages">
                <message :message="message"></message>
            </template>
            <poster-message :cid="state.channel.id"></poster-message>
        </div>
        </div>
    </div>
    </section>
</template>

<style scoped>
.liste-messages {
    margin-bottom: 5vh;
}

.liste-messages {
    display: flex;
    flex-direction: column;
}

.liste-messages .box {
    margin: 0;
}
.btn{
    float: right;
}
 .btn button{
    margin-right: 10px;
 }
 label{
    font-weight: bold;
 }

</style>