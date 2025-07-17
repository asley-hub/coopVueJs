<script setup>
import {useRouter} from 'vue-router';
const router = useRouter();

import {useSessionStore} from '@/stores/session';
const session = useSessionStore();
let member = reactive({
  email: "",
  password: "",
});

function validateFormulaire() {
  console.log(member.email);

  api.post("members/signin", {
      body: member
    })
    .then(response => {
      if(response.message){
        alert(response.message)
      }else{
        session.setSession(response.member, response.token);
        router.push('/');
      }
    })
}
</script>

<template>
  <div class="columns">
    <div class="column is-4 is-offset-4">
    <h1 class="title">Se Connecter</h1>
    <form  class="box" @submit.prevent="validateFormulaire">
      <div class="field">
        <label class="label">Email: </label>
        <input class="input is-primary" type="text" v-model="member.email" placeholder="Votre Email" />
      </div>
      <div class="field">
        <label class="label">Mot de passe: </label>
        <input class="input is-primary"
          type="password"
          v-model="member.password"
          placeholder="Votre mot de passe"
        />
      </div>
      <div class="fied is-grouped">
        <div class="control">
         <button class="button is-primary">Se connecter</button>
        </div>
        <div class="control">
         <router-link to="/creer-un-compte">Annuler</router-link>
        </div>
      </div>
    </form>
    <router-link to="/creer-un-compte">Creer un compte</router-link>
    </div>
  </div>
</template>
