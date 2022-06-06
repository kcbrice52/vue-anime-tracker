<script setup>
import {ref, computed, onMounted } from 'vue';
import MyAnimeList from './components/MyAnimeList.vue'

const query = ref('');
const my_anime = ref([]);
const search_results = ref([]);
let searchView = ref(false);
let myAnimeListView = ref(true);

const searchAnime = () => {
  console.log(query.value)
  const url = 'https://api.jikan.moe/v4/anime?q=' + query.value;
  fetch(url)
    .then(response => response.json())
    .then(response => {
      search_results.value = response.data;
    });
}

const handleInput = e => {
  if (!e.target.value) {
    search_results.value = [];
  }
}

const addAnime = anime => {
  search_results.value = [];
  query.value = '';

  my_anime.value.push({
    id: anime.mal_id,
    title: anime.title,
    image: anime.images.jpg.image_url,
    total_episodes: anime.episodes,
    watched_episodes: 0
  });

  localStorage.setItem('my_anime', JSON.stringify(my_anime.value));
  myAnimeListView.value = true;
  searchView.value = false;
}

const viewSearch = () => {
  searchView.value = true;
  myAnimeListView.value = false;
}

const viewMyAnimeList = () => {
  searchView.value = false;
  myAnimeListView.value = true;
}

onMounted(() => {
 // mounted
 my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || [];
 console.log(my_anime.value);
});
</script>

<template>
  <main>
    <h1>My Anime Tracker</h1>
    <div class="tabs">
      <button class="button" @click="viewSearch">Search</button>
      <button class="button" @click="viewMyAnimeList">
        My Anime <span class="badge" v-if="my_anime.length">{{ my_anime.length }}</span> </button>
    </div>
    <!-- Add the style and icon you want -->
    <font-awesome-icon icon="fa-solid fa-user-secret" />
    <div v-if="searchView">
      <form @submit.prevent="searchAnime">
      <input
        type="text"
        placeholder="Search for an anime..."
        v-model="query"
        @input="handleInput"
      />
      <button type="submit" class="button">Search</button>
    </form>

    <div class="search-results" v-if="search_results.length > 0">
      <div class="result" v-for="anime in search_results" :key="anime">
        <img :src="anime.images.jpg.image_url" />
        <div class="details">
          <h3>{{ anime.title }}</h3>
          <p :title="anime.synopsis" v-if="anime.synopsis">
            {{ anime.synopsis.slice(0, 120) }}...
          </p>
          <span class="flex-1"></span>
          <button @click="addAnime(anime)" class="button">+ Add to my anime</button>
        </div>
      </div>
    </div>
    </div>

    <my-anime-list v-if="myAnimeListView" :my_anime="my_anime"></my-anime-list>
  </main>
  
</template>

