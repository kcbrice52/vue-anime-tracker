<script setup>
import {ref, computed, onMounted } from 'vue';


const my_anime = ref([]);
const episodes = ref([]);

defineProps({
  
})

const count = ref(0);
const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    return a.title.localeCompare(b.title);
  });
});

const increaseWatch = anime => {
  anime.watched_episodes++;
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value));
}

const decreaseWatch = anime => {
  anime.watched_episodes--;
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value));
}

const getEpisodes = anime => {
  console.log(anime.id);
  const url = 'https://api.jikan.moe/v4/anime/' + anime.id + '/episodes';
  fetch(url)
    .then(response => response.json())
    .then(response => {
      console.log(response.data);
      episodes.value = response.data;

    });
}

const remove = anime => {

  const list = JSON.parse(localStorage.getItem('my_anime'));

  console.log(list);
  const animeString = JSON.stringify(anime);
  const animeObject = JSON.parse(animeString);
  const index = list.findIndex((item) => item.id == animeObject.id);
  list.splice(index, 1);
  console.log(list);
  localStorage.setItem('my_anime', JSON.stringify(list));
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || [];
}

onMounted(() => {
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || [];
});
</script>

<template>
  <div class="myanime" v-if="my_anime.length > 0">
      
      
      <h2>My Anime</h2>


      <div v-for="anime in my_anime_asc" :key="anime" class="anime">
          <img :src="anime.image"  />
        <div class="title">
          <h3>{{ anime.title }}</h3>
          <span class="episodes">
          {{ anime.total_episodes }} Episodes
        </span>
        </div>
        <div class="flex-1"></div>
        <!--
        <button :disabled="anime.total_episodes === anime.watched_episodes"
          class="button"
          @click="increaseWatch(anime)">+</button>
        <button
          :disabled="anime.watched_episodes <= 0"
          class="button"
          @click="decreaseWatch(anime)">-</button>-->
        <div class="actions">
          <!--<button
          class="button view-button"
          @click="getEpisodes(anime)">View</button>-->
          <button
          class="item-button positive"
          style="--i:2"
          @click="remove(anime)">	&#10004;</button>
          <button
          class="item-button negative"
          style="--i:1"
          @click="remove(anime)">&times;</button>
        </div>
      </div>
      <div v-for="episode in episodes" :key="episode">
         #{{ episode.mal_id }} - {{ episode.title }}
      </div>
      
    </div>

</template>

<style scoped>


a {
  color: #42b983;
}

.view-button {
  margin-left: 1rem
}

.button:disabled {
  background-color: #ccc;
  background-image: none;
}


</style>
