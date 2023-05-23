<script setup>
import { onMounted, ref } from 'vue'
import { supabase } from '../supabase';
import { useUserStore } from '../stores/users';
import { storeToRefs } from 'pinia';
import Card from './Card.vue';
import Observer from './Observer.vue'

const userStore = useUserStore();
const { user: loggedUser } = storeToRefs(userStore);

const posts = ref([]);
const lastCardIndex = ref(2);
const reachEnd = ref(false);

const ownerIds = ref([]);

const fetchData = async () => {
    const { data: followingIds } = await supabase
        .from("followers_following")
        .select("following_id")
        .eq("follower_id", loggedUser.value.id);

    ownerIds.value = followingIds.map((f) => f.following_id)

    const { data: postsData } = await supabase
        .from("posts")
        .select()
        .in("owner_id", ownerIds.value)
        .range(0, lastCardIndex.value)
        .order("created_at", { ascending: false })
    
    posts.value = postsData;
};

const fetchNextSet = async () => {
    if (!reachEnd.value) {
        const { data: postsData } = await supabase
        .from("posts")
        .select()
        .in("owner_id", ownerIds.value)
        .range(lastCardIndex.value + 1, lastCardIndex.value + 3)
        .order("created_at", { ascending: false })

        posts.value = [...posts.value, ...postsData];

        lastCardIndex.value += 3;

        if (!postsData.length) {
            reachEnd.value = true;
        }
    }
}

onMounted(async () => {
    fetchData();
})
</script>

<template>
    <div class="timeline-container">
        <Card 
            v-for="post in posts" 
            :key="post.id" 
            :post="post" 
        />
        <Observer v-if="posts.length" @intersect="fetchNextSet"/>
    </div>
</template>