<template>
    <v-container style="margin-bottom: 32px;">
        <v-card v-if="news" class="mb-6">
            <v-card-title class="text-h6">{{ news.title }}</v-card-title>

            <v-card-text>
                <v-row>
                    <v-col>
                        <a :href="news.url" target="_blank" class="text-decoration-none text-primary">
                            Ссылка на новость
                        </a>
                    </v-col>
                </v-row>
                <v-row>
                    <v-col cols="12" sm="6">
                        <p>Автор: <strong>{{ news.by }}</strong></p>
                    </v-col>
                    <v-col cols="12" sm="6" v-if="news.time">
                        <p>Дата: <strong>{{ formattedDate }}</strong></p>
                    </v-col>
                </v-row>
                <v-row>
                    <v-col>
                        <p>Комментарии: <strong>{{ news.descendants }}</strong></p>
                    </v-col>
                </v-row>
            </v-card-text>

            <v-card-actions>
                <v-btn @click="fetchNews" color="primary" outlined>
                    Обновить
                </v-btn>
                <v-btn @click="goHome" color="primary" outlined>
                    Домой
                </v-btn>
            </v-card-actions>

            <v-divider></v-divider>

            <v-card class="mt-4" v-for="id in news.kids" :key="id" outlined>
                <Comment :commentId="id" />
            </v-card>
        </v-card>
    </v-container>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';
import Comment from '../components/Comment.vue';
import { useRoute, useRouter } from 'vue-router';
import type { Item } from '@/types/item';

const router = useRouter();
const route = useRoute();
const news = ref<Item>();
const fetchNews = async () => {
    try {
        const { id } = route.params;
        const { data } = await axios.get(`https://hacker-news.firebaseio.com/v0/item/${id}.json`);
        news.value = data;
    } catch (error) {
        console.error('Ошибка загрузки новости:', error);
    }
}

const formattedDate = computed(() => {
    if (news.value && news.value.time)
        return new Date(news.value.time * 1000).toLocaleString();
});

const goHome = () => {
    router.push('/');
}

onMounted(async () => {
    fetchNews();
});

</script>