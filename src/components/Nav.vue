<script setup>
import { RouterLink, useRouter } from "vue-router";
import Container from "./Container.vue";
import AuthModal from "./AuthModal.vue";
import { ref } from "vue";
import { useUserStore } from '../stores/users';
import { storeToRefs } from 'pinia';

const router = useRouter();
const searchUsername = ref("");
const isAuthenticated = ref(false);
const userStore = useUserStore();

const { user, loadingUser } = storeToRefs(userStore);

const onSearch = () => {
    if (searchUsername.value) {
        router.push(`/profile/${searchUsername.value}`);
        searchUsername.value = "";
    }
};

const handleLogout = async () => {
    await userStore.handleLogout();
}

const goToUsersProfile = () => {
    router.push(`/profile/${user.value.username}`);
}
</script>
<template>
    <ALayoutHeader>
      <Container>
        <div class="nav-container">
            <div class="right-content">
                <RouterLink to="/">Instagram</RouterLink>
                <AInputSearch
                    v-model:value="searchUsername"
                    placeholder="Username..."
                    style="width: 200px"
                    @search="onSearch"
                />
            </div>
            <div class="left-content" v-if="!loadingUser">
                <div v-if="!user" class="left-content">
                    <AuthModal :isLogin="false" />
                    <AuthModal :isLogin="true" />
                </div>
                <div v-else class="left-content">
                    <AButton type="primary" @click="goToUsersProfile">Profile</AButton>
                    <AButton type="primary" @click="handleLogout">Logout</AButton>
                </div>
            </div>
        </div>
      </Container>
    </ALayoutHeader>
</template>

<style scoped>
.nav-container {
    display: flex;
    justify-content: space-between;
}

.right-content {
    display: flex;
    align-items: center;
    gap: 10px;
}

.left-content {
    display: flex;
    align-items: center;
    gap: 10px;
}
</style>