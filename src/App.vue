<script setup>
import { ref, computed, onMounted } from "vue";

const query = ref("");
const my_anime = ref([]);
const search_results = ref([]);

const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    return a.title.localeCompare(b.title);
  });
});

const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`;
  fetch(url)
    .then((res) => res.json())
    .then((res) => {
      search_results.value = res.data;
    });
};

const handleInput = (e) => {
  if (!e.target.value) {
    search_results.value = [];
  }
};

const addAnime = (anime) => {
  search_results.value = [];
  query.value = "";

  my_anime.value.push({
    id: anime.mal_id,
    title: anime.title,
    image: anime.images.jpg.image_url,
    total_episodes: anime.episodes,
    watched_episodes: 0,
  });
  localStorage.setItem("my_anime", JSON.stringify(my_anime.value));
};

const increaseWatch = (anime) => {
  anime.watched_episodes++;
  localStorage.setItem("my_anime", JSON.stringify(my_anime.value));
};
const decreaseWatch = (anime) => {
  anime.watched_episodes--;
  localStorage.setItem("my_anime", JSON.stringify(my_anime.value));
};

onMounted(() => {
  my_anime.value = JSON.parse(localStorage.getItem("my_anime")) || [];
});
</script>

<template>
  <div class="animeTrack">
  <main>
    <h1> ANIME<span class="header">TRACKER</span> </h1>
    <form @submit.prevent="searchAnime">
      <input
        type="text"
        placeholder="Search for anime..."
        v-model="query"
        @input="handleInput"
      />
      <button type="submit" class="button">Search</button>
    </form>
    <div class="results" v-if="search_results.length > 0">
      <div class="result" v-for="anime in search_results" :key="anime.id">
        <img :src="anime.images.jpg.image_url" />
        <div class="details">
          <h3>{{ anime.title }}</h3>
          <p :title="anime.synopsis" v-if="anime.synopsis">
            {{ anime.synopsis.slice(0, 120) }}
          </p>
          <span class="flex-1"></span>
          <button @click="addAnime(anime)" class="button" >Add to my list</button>
        </div>
      </div>
    </div>
    <div class="myanime" v-if="my_anime.length > 0">
      <h2>My Anime</h2>
      <div class="anime" v-for="anime in my_anime_asc" :key="anime.id">
        <img :src="anime.image" />
        <h3>{{ anime.title }}</h3>
        <div class="flex-1"></div>
        <span class="episodes">
          {{ anime.watched_episodes }} / {{ anime.total_episodes }}
        </span>
        <button class="button"
          v-if="anime.total_episodes !== anime.watched_episodes"
          @click="increaseWatch(anime)"
        >
          +
        </button>
        <button class="button"
          v-if="anime.watched_episodes > 0"
          @click="decreaseWatch(anime)"
        >
          -
        </button>
      </div>
    </div>
  </main>
</div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Niramit:wght@200;400;600&display=swap');

*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Niramit', sans-serif;
  
}
.animeTrack{
  background-color: #eee;
}
main{
  margin: 0 auto;
  max-width: 768px;
  padding: 1.5rem;
}

h1{
  text-align: center;
  margin-bottom: 1.5rem;
  text-shadow: 0 5px 5px rgba(111, 111, 111, 0.25);
  color: rgb(66,66,66);
}
.header{
  background: rgb(65, 65, 65);
  color:rgb(255, 229, 36) ;
  box-shadow: 0 2px 7px rgba(0,0,0,0.55);
  border-radius: 2.5px;
  padding: 5px;
}
form{
  display: flex;
  max-width: 480px;
  margin: 0 auto 1.5rem;
}

form input{
  appearance: none;
  outline: none;
  border: none;
  background: white;

  display: block;
  color: #888;
  font-size: 1.125rem;
  padding: 0.5rem 1rem;
  width: 100%;
}
.button{
  appearance: none;
  outline: none;
  border: none;
  background: none;
  cursor: pointer;
  border-radius: 5px;

  display: block;
  padding: 0.5rem 1rem;
  background-image:  linear-gradient(to right, rgb(91, 91, 91) 50%,rgb(255, 222, 57) 50%);
  background-size: 200%;
  color: #fff;
  font-size: 1.125rem;
  font-weight: 700;
  text-transform: uppercase;
  transition: 0.4s;
}
.button:hover{
  background-position: right;
}
.results{
  background-color: #fff;
  border-radius:0.5rem ;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  max-height: 480px;
  overflow-y: scroll;
  margin-bottom: 1.5rem;
}
.result{
  display: flex;
  margin: 1rem;
  padding: 1rem;
  border: 1px solid#ccc;
  border-radius: 0.5rem;
  transition:0.4s ;
}
.result img{
  width: 100px;
  border-radius: 1rem;
  margin-right: 1rem;
}
.details{
  flex: 1 1 0%;
  display: flex;
  align-items: flex-start;
  flex-direction: column;
}
.flex-1{
  flex: 1 1 0%;
}
.details h3{
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
}
.details p{
  font-size: 0.875rem;
  line-height: 1.4;
  margin-bottom: 0.5rem;
}
.details .button{
  margin-left: auto;
}
.myanime h2{
  color: #888;
  font-weight: 400;
  margin-bottom: 1.5rem;
}
.myanime .anime{
  display: flex;
  align-items: center;
  margin-bottom: 1.5rem;
  background-color: #fff;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
.myanime img{
  width: 72px;
  height: 72px;
  object-fit: cover;
  border-radius: 1rem;
  margin-right: 1rem;
}
.anime h3{
  color: #888;
  font-size: 1.125rem;
}
.anime .episodes{
  margin-right: 1rem;
  color: #888;
}
.anime .button:first-of-type{
  margin-right: 1rem;
}
.anime .button:last-of-type{
  margin-right: 0;
}

</style>
