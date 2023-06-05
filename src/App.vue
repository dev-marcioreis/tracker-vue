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
          <h1>encontrando animes</h1>
          <form @submit.prevent="searchAnime">
            <input type="text" placeholder="Procurar anime..." v-model="queryAnime" @input="handleInput" class="anime-input">
            <button type="submit" class="btn">procurar</button>
          </form>
        </div>

        <div class="results" v-if="searchAnimeResults.length > 0">
          <h2>Animes encontrados</h2>
          <div class="results-container">
            <div class="result" v-for="(anime, index) in searchAnimeResults" v-bind:key="index">
              <img :src="anime.images.jpg.image_url" />
              <div class="details">
                <h3>{{ anime.title }}</h3>
                <p :title="anime.synopsis" v-if="anime.synopsis">{{ anime.synopsis.slice(0, 280) }}. . .</p>
                <button @click="addAnime(anime)" class="btn">Add na minha lista</button>
              </div>
            </div>
          </div>
        </div>

        <div class="myanime" v-if="myAnime.length > 0">
          <h2>Meus animes</h2>
          <div class="animes-container">
            <div v-for="(anime, index) in myAnimeAscending" v-bind:key="index" class="anime">
              <img :src="anime.image" />
              <h3>{{ anime.title }}</h3>
              <span class="episodes">
                {{ anime.watchedEpisodes }} / {{ anime.totalEpisodes }}
              </span>
              <div class="btn-animes">
                <button v-if="anime.totalEpisodes !== anime.watchedEpisodes" @click="increaseWatch(anime)" class="btn-anime">
                  +
                </button>
                <button v-if="anime.watchedEpisodes > 0" @click="decreaseWatch(anime)" class="btn-anime">
                  -
                </button>
              </div>
            </div>
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
      --white-color: hsl(0, 0%, 100%);
      --gradient-color: linear-gradient(90deg, hsl(355, 37%, 88%) 0%, hsl(25, 76%, 90%) 100%);
      
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
      widows: 100%;
      background: var(--light-color);
      box-shadow: var(--shadow);
      padding: .5rem;
      margin-right: .5rem;
    }
    .btn {
      font-weight: 500;
      padding: .5rem .7rem;
      background: var(--gradient-color);
      color: var(--black-color);
      text-transform: uppercase;
      box-shadow: var(--shadow);
      cursor: pointer;
      transition: var(--transition);
    }
    .btn:hover {
      box-shadow: var(--shadow-1);
    }
    .results {
      margin-block-start: 7rem;
    }
    .results h2 {
      margin-block-end: 2rem;
      color: var(--primary-color);
      font-weight: 300;
      border-bottom: 1px solid var(--primary-color);
    }
    .results-container {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      place-items: center;
      gap: 1.5rem;
      margin-block-end: 2rem;
    }
    .result {
      display: flex;
      align-items: center;
      justify-content: space-between;
      flex-direction: column;
      width: 100%;
      height: 100%;
      padding: .5rem;
      background: var(--white-color);
      box-shadow: var(--shadow);
    }
    .details {
      margin-block-start: 1rem;
      padding: .5rem;
      text-align: center;
    }
    .details h3 {
      margin-block-end: .7rem;
      font-size: 1rem;
      font-weight: 600;
    }
    .details p {
      font-size: .9rem;
      margin-block-end: 1.5rem;
    }
    .myanime {
      text-align: left;
      margin-block-start: 6rem;
    }
    .myanime h2 {
      margin-block-end: 2rem;
      color: var(--primary-color);
      font-weight: 300;
      border-bottom: 1px solid var(--primary-color);
    }
    .animes-container {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 2rem;
      margin-block-end: 2rem;
    }
    .anime {
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      text-align: center;
      widows: 100%;
      height: 100%;
    }
    .anime h3 {
      margin-block-start: 1rem;
      margin-block-end: 1rem;
      font-size: 1rem;
      font-weight: 600;
    }
    .btn-animes {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 1rem;
      margin-block-start: 1rem;
    }
    .btn-anime {
      padding: .2rem .7rem;
      background: var(--gradient-color);
      box-shadow: var(--shadow);
      cursor: pointer;
      margin-block-end: 1rem;
    }
    

  /*==========Media Query Tablet==========*/
    @media (max-width: 992px) {
      .container{
        width: var(--container-md);
      }
      .anime__top h1 {
        font-size: 1.5rem;
      }

    }

  /*==========Media Query Mobile==========*/
    @media (max-width: 768px) {
      .anime__top h1 {
        display: none;
      }

      .anime__top .btn {
        display: none;
      }

    }
    
</style>
