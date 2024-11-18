<template>
    <v-card class="mb-4">
        <v-card-title>
            <v-btn :href="story.url" target="_blank" variant="text" color="primary">
                {{ story.title }}
            </v-btn>
        </v-card-title>
        <v-card-subtitle>
            <v-row>
                <v-col cols="auto">
                    <v-icon class="mr-1">mdi-thumb-up</v-icon>{{ story.score }}
                </v-col>
                <v-col cols="auto">
                    <v-icon class="mr-1">mdi-account</v-icon>{{ story.by }}
                </v-col>
                <v-col cols="auto">
                    <v-icon class="mr-1">mdi-calendar</v-icon>{{ formattedDate }}
                </v-col>
            </v-row>
        </v-card-subtitle>
        <v-card-actions>
            <v-btn @click="goToDetails" color="secondary" variant="tonal">
                Подробнее
            </v-btn>
        </v-card-actions>
    </v-card>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { useRouter } from 'vue-router';
import type { Item } from '@/types/item';

const props = defineProps<{
    story: Item
}>();
const router = useRouter();

const formattedDate = computed(() => {
    if (props.story.time)
        return new Date(props.story.time * 1000).toLocaleString();
});

const goToDetails = () => {
    router.push(`/news/${props.story.id}`);
};

</script>