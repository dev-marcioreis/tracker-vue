<script setup>

  import { ref, computed, onMounted } from 'vue';
  
  const queryAnime = ref('');
  const myAnime = ref([]);
  const searchAnimeResults = ref([]);

  const myAnimeAscending = computed(() => {

    return myAnime.value.sort((a, b) => {
      return a.title.localeCompare(b.title)
    })

  });

  const searchAnime = () => {

    const url = `https://api.jikan.moe/v4/anime?q=${queryAnime.value}`

    fetch(url).then(res => res.json()).then(res => {
      searchAnimeResults.value = res.data
    })

  };

  const handleInput = e => {
    
    if(!e.target.value) {
      searchAnimeResults.value = []
    }

  };

  const addAnime = anime => {

    searchAnimeResults.value = []
    queryAnime.value = ''

    myAnime.value.push({
      id: anime.mal_id,
      title: anime.title,
      image: anime.images.jpg.image_url,
      totalEpisodes: anime.episodes,
      watchedEpisodes: 0
    })

    localStorage.setItem('myAnime', JSON.stringify(myAnime.value))

  };

  const increaseWatch = anime => {
    
    anime.watchedEpisodes++
    localStorage.setItem('myAnime', JSON.stringify(myAnime.value))

  };

  const decreaseWatch = anime => {
    
    anime.watchedEpisodes--
    localStorage.setItem('myAnime', JSON.stringify(myAnime.value))

  };

  onMounted(() => {

    myAnime.value = JSON.parse(localStorage.getItem('myAnime')) || []

  });

</script>



<template>

  <main>
    <div class="container">

        <div class="anime__top">
          <h1>encotrando animes</h1>
          <form @submit.prevent="searchAnime">
            <input type="text" placeholder="Procurar anime..." v-model="queryAnime" @input="handleInput" class="anime-input">
            <button type="submit" class="search-btn">procurar</button>
          </form>
        </div>

        <div class="results" v-if="searchAnimeResults.length > 0">
          <div class="result" v-for="(anime, index) in searchAnimeResults" v-bind:key="index">
            <img :src="anime.images.jpg.image_url" />
            <div class="details">
              <h3>{{ anime.title }}</h3>
              <p :title="anime.synopsis" v-if="anime.synopsis">{{ anime.synopsis.slice(0, 280) }}. . .</p>
              <span class="flex-1"></span>
              <button @click="addAnime(anime)">Add na minha lista</button>
            </div>
          </div>
        </div>

        <div class="myanime" v-if="myAnime.length > 0">
          <h2>Meus animes</h2>
          <div v-for="(anime, index) in myAnimeAscending" v-bind:key="index" class="anime">
            <img :src="anime.image" />
            <h3>{{ anime.title }}</h3>
            <div class="flex-1"></div>
            <span class="episodes">{{ anime.watchedEpisodes }} / {{ anime.totalEpisodes }}</span>
            <button v-if="anime.totalEpisodes !== anime.watchedEpisodes" @click="increaseWatch(anime)">
              +
            </button>
            <button v-if="anime.watchedEpisodes > 0" @click="decreaseWatch(anime)">
              -
            </button>
          </div>
        </div>

    </div>
  </main>

</template>



<style>

  /*==========Google Fonts==========*/
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@100;200;300;400;500;600;700;800;900&display=swap');

  /*==========Reset==========*/
    *, *::before, *::after {
        margin: 0;
        padding: 0;
        border: 0;
        box-sizing: border-box;
        outline: none;
        text-decoration: none;
        list-style: none;
        appearance: none;
        font-family: 'Poppins', sans-serif;
    }

  /*==========Root==========*/
    :root {

      --primary-color: hsl(347, 100%, 71%);
      --dark-color: hsl(0, 0%, 13%);
      --black-color: hsl(0, 0%, 0%);
      --light-color: hsl(210, 40%, 96%);
      
      --transition: all 400ms ease-in-out;
      --shadow: 0px 1px 3px hsla(0, 0%, 0%, 0.5);
      --shadow-1: 0px 3px 6px hsla(0, 0%, 0%, 0.4);

      --container-lg: 80%;
      --container-md: 90%;
      --container-max-wd: 1800px;

    }

  /*==========Base==========*/
    body {
      background: var(--light-color);
    }

    ::-webkit-scrollbar {
      width: .5rem;
    }
    ::-webkit-scrollbar-track {
      background: var(--dark-color)
    }
    ::-webkit-scrollbar-thumb {
      background: var(--primary-color);
    }

    .container {
      width: var(--container-lg);
      max-width: var(--container-max-wd);
      margin-inline: auto;  
    }

  /*==========Anime Container==========*/
    .anime__top {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      height: 3.2rem;
      background: var(--primary-color);
      box-shadow: var(--shadow-1);
      display: flex;
      align-items: center;
      justify-content: space-around;
    }
    .anime__top h1 {
      font-size: 2rem;
      font-weight: 300;
      color: var(--light-color);
      text-transform: uppercase;
      text-shadow: var(--shadow-1);
    }
    .anime-input {
      background: var(--light-color);
      box-shadow: var(--shadow);
      padding: .5rem;
      margin-right: .5rem;
      border-radius: .5rem;
    }
    .search-btn {
      padding: .5rem;
      border-radius: .5rem;
      box-shadow: var(--shadow);
      cursor: pointer;
      text-transform: capitalize;
      transition: var(--transition);
    }
    .search-btn:hover {
      color: var(--primary-color);
      box-shadow: var(--shadow-1);
    }

  /*==========Media Query Tablet==========*/
    @media (max-width: 992px) {
      .container{
        width: var(--container-md);
      }

    }
    
</style>
