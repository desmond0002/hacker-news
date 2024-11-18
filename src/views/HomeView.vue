<template>
  <v-card>
    <v-card-title>Hacker News</v-card-title>
    <v-card-actions>
      <v-btn @click="fetchStories">Обновить список</v-btn>
    </v-card-actions>
    <v-card v-if="isLoading" class="text-center">
      <v-card-text>
        <v-progress-circular indeterminate color="primary"></v-progress-circular>
        Загрузка...
      </v-card-text>
    </v-card>
    <v-list v-else>
      <ItemCard v-for="story in storiesList" :story="story" :key="story.id" @click="goToNews(story.id)" />
    </v-list>
  </v-card>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import axios from 'axios';
import ItemCard from '@/components/Item.vue';
import { useRouter } from 'vue-router';
import type { Item } from '@/types/item';

const storiesList = ref<Array<Item>>([]);
const isLoading = ref(true);
const router = useRouter();
const fetchStories = async () => {
  try {
    const { data: ids } = await axios.get<Array<number>>('https://hacker-news.firebaseio.com/v0/newstories.json');
    const top100 = ids.slice(0, 100);
    storiesList.value = await Promise.all(
      top100.map(async (id: number) => {
        const { data } = await axios.get(`https://hacker-news.firebaseio.com/v0/item/${id}.json`);
        return data;
      })
    );
    if (storiesList.value.length > 1)
      storiesList.value.sort((a, b) => {
        if (b.time && a.time) return b.time - a.time;
        else return 0;
      });
    isLoading.value = false;
  } catch (error) {
    console.error('Ошибка загрузки новостей:', error);
  }
};

onMounted(() => {
  fetchStories();
  setInterval(fetchStories, 60000);
});

const goToNews = (id: number) => {
  router.push(`/news/${id}`);
};

</script>
