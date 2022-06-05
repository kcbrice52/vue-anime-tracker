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

    <div class="results" v-if="search_results.length > 0">
      <div class="result" v-for="anime in search_results" :key="anime">
        <img :src="anime.images.jpg.image_url" />
        <div class="details">
          <h3>{{ anime.title }}</h3>
          <p :title="anime.synopsis" v-if="anime.synopsis">
            {{ anime.synopsis.slice(0, 120) }}...
          </p>
          <span class="flex-1"></span>
          <button @click="addAnime(anime)" class="button">Add to my anime</button>
        </div>
      </div>
    </div>
    </div>

    <my-anime-list v-if="myAnimeListView" :my_anime="my_anime"></my-anime-list>
  </main>
  
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Fira Sans', sans-serif;
}

html {
  background-color: #eee;
  background-image: url('./assets/Anime.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed;
  height: 100%
}

main {
  margin: 1rem auto 1rem auto;
  max-width: 768px;
  padding: 1.5rem;
  background-color: rgba(256, 256, 256, 0.9);
}

h1 {
  text-align: center;
  margin-bottom: 1.5rem;
}

form {
  display: flex;
  max-width: 480px;
  margin: 0 auto 1.5rem;
}

form input {
  appearance: none;
  outline: none;
  border: none;
  background: #fff;

  display: block;
  color: #888;
  font-size: 1.125rem;
  padding: 0.5rem 1rem;
  width: 100%;
}

.button {
  appearance: none;
  outline: none;
  border: none;
  background: none;
  cursor: pointer;

  display: block;
  padding: 0.5rem 1rem;
  background-image: linear-gradient(to right, #c64369 50%, #35427d 50%);
  background-size: 200%;
  color: #fff;
  font-size: 1.125rem;
  font-weight: 700;
  text-transform: uppercase;
  transition: 0.4s;
}

.button:hover,
.button:focus {
  background-position: right;
}

.results {
  background: #fff;
  border-radius: 0.5rem;
  box-shadow: 0 2px 4px rgba(53,66, 125, 0.1);
  max-height: 650px;
  overflow-y: scroll;
  margin-bottom: 1.5rem;
}

.result {
  display: flex;
  margin: 1rem;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 0.5rem;
  transition: 0.4s;
}

.result img {
  width: 100px;
  border-radius: 1rem;
  margin-right: 1rem;
}

.details {
  flex: 1 1 0%;
  display: flex;
  align-items: flex-start;
  flex-direction: column;
}

.flex-1 {
  display:block;
  flex: 1 1 0%;
}

.details h3 {
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
}

.details p {
  font-size: 0.875rem;
  line-height: 1.4;
  margin-bottom: 0.5rem;
}

.details .button {
  margin-left: auto;
}

.myanime {
  padding: 1rem 0rem;
}

.myanime h2 {
  color: #333;
  font-weight: 400;
  margin-bottom: 1.5rem;
}

.myanime .anime {
  display: flex;
  align-items: center;
  margin-bottom: 1.5rem;
  background-color: #fff;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
}

.anime img {
  width: 72px;
  height: 72px;
  object-fit: cover;
  border-radius: 1rem;
  margin-right: 1rem;
}

.anime h3 {
  color: #888;
  font-size: 1.125rem;
}

.anime .episodes {
  margin-right: 1rem;
  color: #888;
}

.anime .button:first-of-type {
  margin-right: 1rem;
}

.anime .button:last-of-type { 
  margin-right: 0;
}

.tabs {
  display: flex;
  margin: 0 auto;
  align-items: center;
  margin-bottom: 1.5rem;
  background-color: #fff;
  padding: 0rem;
  border-radius: 0.5rem;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  width: fit-content
}
.tabs .button {
  border: 1px solid #fff;
  border-radius: 0.5rem;
}

.tabs .button:first-of-type {
  margin-right: 0rem;
  border-top-right-radius: 0rem;
  border-bottom-right-radius: 0rem;
  

}


.tabs .button:first-of-type:hover,
.tabs .button:first-of-type:focus {
  margin-right: 0rem;
  border-top-right-radius: 0  rem;
  border-bottom-right-radius: 0 rem;

}

.tabs .button:last-of-type { 
  margin-right: 0;
  border-top-left-radius: 0rem;
  border-bottom-left-radius: 0rem;
  background-image: linear-gradient(to right, #35427d 50%, #c64369 50%);
  background-position: right;
}

.tabs .button:last-of-type:hover,
.tabs .button:last-of-type:focus { 
  margin-right: 0;
  border-top-left-radius: 0 rem;
  border-bottom-left-radius: 0  rem;
  background-position: left;
}
.button .badge {
  background: white;
  color: black;
  border-radius: 3rem;
  font-size: 0.65rem;
  padding: 0.28rem 0.5rem;
  width: 20px;
  height: 20px;
  line-height: 1em;
  margin-left: 0.5rem;
}






</style>
