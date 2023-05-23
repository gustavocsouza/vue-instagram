<script setup>
import { reactive, ref } from 'vue';
import { useUserStore } from '../stores/users';
import { storeToRefs } from "pinia"; 

const userStore = useUserStore();
const { errorMessage, loading, user } = storeToRefs(userStore);

const { isLogin } = defineProps(['isLogin']);

const visible = ref(false);

const userCredentials = reactive({
    email: "",
    password: "",
    username: ""
});

const showModal = () => {
    visible.value = true;
};

const clearUserCredentialsInput = () => {
    userCredentials.email = "";
    userCredentials.password = "";
    userCredentials.username = "";
    userStore.clearErrorMessage();
}

const handleOk = async () => {
    if (isLogin) {
        await userStore.handleLogin({
            password: userCredentials.password,
            email: userCredentials.email
        });
    } else {
        await userStore.handleSignup(userCredentials);
    }

    if (user.value) {
        clearUserCredentialsInput();
        visible.value = false;
    }
};

const handleCancel = () => {
    clearUserCredentialsInput();
    visible.value = false;
}

const title =  isLogin ? "Login" : "Signup" ;
</script>
<template>
<div>
    <AButton 
        type="primary" 
        class="btn"
        @click="showModal"
    >
        {{title}}
    </AButton>
    <AModal v-model:visible="visible" :title="title">
        <div class="form-container">
            <div v-if="!loading" class="input-container">
                <AInput 
                    v-if="!isLogin" 
                    v-model:value="userCredentials.username" 
                    placeholder="Username" 
                />
                <AInput v-model:value="userCredentials.email" placeholder="Email" />
                <AInput 
                    v-model:value="userCredentials.password" 
                    placeholder="Password" 
                    type="password"
                />
            </div>
            <div v-else class="spinner">
                <ASpin />
            </div>
            <ATypographyText v-if="errorMessage" type="danger">{{ errorMessage }}</ATypographyText>
        </div>
        <template #footer>
            <AButton key="back" @click="handleCancel">Cancel</AButton>
            <AButton 
                :disabled="loading"
                key="submit" 
                type="primary" 
                :loading="loading"
                @click="handleOk"
            >
                Submit
            </AButton>
        </template>
    </AModal>
</div>
</template>

<style scoped>
.input-container  {
    display: flex;
    flex-direction: column;
    gap: 5px;
    height: 120px;
}

.spinner {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 120px;
}
</style>