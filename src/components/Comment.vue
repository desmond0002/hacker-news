<template>
  <v-card outlined class="pa-4 mb-4">
    <v-card-title>
      <v-icon class="mr-2">mdi-account</v-icon>
      {{ comment?.by }}
    </v-card-title>
    <v-card-text v-html="comment?.text" />
    <v-card-actions>
      <v-btn v-if="comment?.kids && !kidsLoaded" @click="loadKids" variant="outlined" size="small" color="primary">
        Показать ответы
      </v-btn>
    </v-card-actions>
    <v-container v-if="kidsLoaded" class="pl-4">
      <Comment v-for="childId in children" :key="childId" :comment-id="childId" :depth="depth + 1" />
    </v-container>
  </v-card>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import axios from "axios";

const props = defineProps({
  commentId: {
    type: Number,
    required: true,
  },
  depth: {
    type: Number,
    default: 0,
  },
});

const comment = ref<any>(null);
const children = ref<number[]>([]);
const kidsLoaded = ref(false);

const loadComment = async () => {
  try {
    const response = await axios.get(`https://hacker-news.firebaseio.com/v0/item/${props.commentId}.json`);
    comment.value = response.data;
  } catch (error) {
    console.error(`Error loading comment ${props.commentId}:`, error);
  }
};

const loadKids = async () => {
  if (comment.value?.kids) {
    children.value = comment.value.kids;
    kidsLoaded.value = true;
  }
};

onMounted(loadComment);

defineExpose({
  name: "Comment",
});
</script>

<style scoped>
.v-container {
  border-left: 2px solid rgba(0, 0, 0, 0.1);
  margin-left: 16px;
  padding-left: 16px;
}
</style>
