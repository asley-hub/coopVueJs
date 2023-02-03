<script setup>
import { useSessionStore } from '@/stores/session'
import { onMounted, reactive } from 'vue'
import { useRouter } from 'vue-router';

const router = useRouter();
const session = useSessionStore();

const state = reactive({
    menuOuvert: false
});
function logOut() {
    if(!confirm('Voulez-vous vraiment vous déconnecter ?')) return;
    session.endSession();
   router.push('/se-connecter');
}
</script>
<template>
    <nav class="navbar" role="navigation" aria-label="main navigation">
        <div class="navbar-brand">
            <router-link to="/" class="navbar-item">
                <img src="data:image/png;base64,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" height="28">
            </router-link>

            <button role="button" class="navbar-burger" @click="state.menuOuvert=!state.menuOuvert">
                <span aria-hidden="true"></span>
                <span aria-hidden="true"></span>
                <span aria-hidden="true"></span>
            </button>
        </div>

        <div class="navbar-menu" :class="{'is-active':state.menuOuvert}">
            <div class="navbar-start">
                <router-link to="/" class="navbar-item">
                    Conversations
                </router-link>

                <router-link to="/liste-membres" class="navbar-item">
                    Membres
                </router-link>


            </div>

            <div class="navbar-end">
                <div class="navbar-item">
                    <div class="buttons">
                        <button @click="logOut" class="button is-danger">
                            <strong>Se déconnecter</strong>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </nav>
</template>