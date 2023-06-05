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

  <main class="main">
    <div class="container">
      <h1>Em desenvolvimento...</h1>
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

  /*==========Media Query Tablet==========*/
    @media (max-width: 992px) {
      .container{
        width: var(--container-md);
      }

    }
    
</style>
