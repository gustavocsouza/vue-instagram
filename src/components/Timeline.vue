<script setup>
import Container from './Container.vue';
import Cards from './Cards.vue';
import LogInMessage from './LogInMessage.vue';
import { useUserStore } from '../stores/users';
import { storeToRefs } from 'pinia';

const userStore = useUserStore();
const { user: loggedUser, loadingUser } = storeToRefs(userStore);
</script>

<template>  
    <Container>
        <div v-if="!loadingUser">            
            <Cards v-if="loggedUser" />
            <LogInMessage v-else />
        </div>
        <div v-else class="timeline-spinner">
            <ASpin />
        </div>
    </Container>
</template>

<style>
.timeline-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px 0;
    gap: 20px;
}

.timeline-spinner {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 50vh;
}
</style>