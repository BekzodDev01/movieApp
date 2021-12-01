<template>
  <div class="home">
    <!-- Hero -->
    <Hero />
    <!-- Search  -->
    <div class="container search">
      <input type="text" @keyup.enter='$fetch' placeholder="Search" 
      v-model.lazy="searchInput">
      <button  @click="clearSearch" v-show="searchInput !== ''" class="button">Clear Search</button>
    </div>


    <!-- Movies -->
    <div class="container movies" v-if="searchInput !=='' ">
      <div class="movies-grid" id="movies-grid">
        <div class="movie" v-for="(movie, index) in searchedMovies" :key='index'>
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="The image">

            <p class="review">{{movie.vote_average}} </p>
            <p class="overview">{{movie.overview}} </p>
          </div>

          <div class="info">
            <p class="title">
              {{movie.title.slice(0, 25)}}
              <span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Release:
              {{
              new Date(movie.release_date).toLocaleString('en-US', {
              month: 'long',
              day: 'numeric',
              year: 'numeric',
              })
              }}
            </p>
             <NuxtLink
            class="button button-light"
            :to="{ name: 'movies-movieid', params: { id: movie.id } }"
          >
            Get More Info
          </NuxtLink>
          </div>

        </div>
      </div>
    </div>
    <div class="container movies" v-else>
      <div class="movies-grid" id="movies-grid">
        <div class="movie" v-for="(movie, index) in movies" :key='index'>
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="The image">

            <p class="review">{{movie.vote_average}} </p>
            <p class="overview">{{movie.overview}} </p>
          </div>

          <div class="info">
            <p class="title">
              {{movie.title.slice(0, 25)}}
              <span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Release:
              {{
              new Date(movie.release_date).toLocaleString('en-US', {
              month: 'long',
              day: 'numeric',
              year: 'numeric',
              })
              }}
            </p>
            <NuxtLink
            class="button button-light"
            :to="{ name: 'movies-movieid', params: { id: movie.id } }"
          >
            Get More Info
          </NuxtLink>
          </div>

        </div>
      </div>
    </div>


  </div>
</template>

<script>
  import axios from 'axios'
  export default {
    head(){
      return {
        title: 'Movie App Latest Streaming',
        meta:[
          {
            hid:'Description',
            name: 'Description',
            content: 'Description'
          },
          {
            hid:'keywords',
            name: 'keywords',
            content: 'movies, stream, streaming'
          }
        ]

      }
    },


    data() {
      return {
        movies: [],
        searchInput: '',
        searchedMovies: []
      }
    },



    async fetch() {
      if (this.searchInput == '') {
        await this.getMovies()
        return
      }
       await this.searchMovies()

    },
    methods: {
      async getMovies() {
        const getData = axios.get('https://api.themoviedb.org/3/movie/now_playing?api_key=37ed43a4f8eaa2abd75f9283692947bc&language=en-US&page=1')
        const result = await getData
        result.data.results.forEach(movie => {
          this.movies.push(movie)
        })

        console.log(this.movies);

      },

      async searchMovies() {
        const data = axios.get(
          `https://api.themoviedb.org/3/search/movie?api_key=37ed43a4f8eaa2abd75f9283692947bc&language=en-US&page=1&query=${this.searchInput}`
        )
        const result = await data
        result.data.results.forEach((movie) => {
          this.searchedMovies.push(movie)
        })
        console.log(this.searchedMovies);
      },
   
      clearSearch(){
        this.searchInput=''
        this.searchedMovies=[]
      }
   
    },


  }
</script>

<style lang="scss">
  .home {
    background-color: #211f1f;
  }

  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }

  input {
    max-width: 350px;
    width: 100%;
    padding: 12px 6px;
    font-size: 14px;
    border: none;
    border-radius: 6px;
  }

  input:focus {
    outline: none;
    border-bottom-right-radius: 0;

  }

  .search {
    display: flex;
    padding: 32px 16px;
    align-items: top;
    
  }

  .button {
    border-radius: 2px;
    border-top-left-radius: 0;
    border-bottom-left-radius: 0;
  }

  .movies {
    padding: 32px 16px;
  }

  .movies-grid {
    display: grid;
    column-gap: 32px;
    row-gap: 64px;
    grid-template-columns: 1fr;

    @media (min-width: 500px) {
      grid-template-columns: repeat(2, 1fr);
    }

    @media (min-width: 750px) {
      grid-template-columns: repeat(2, 1fr);
    }

    @media (min-width: 1100px) {
      grid-template-columns: repeat(4, 1fr);
    }
  }

  .movie {
    position: relative;
    display: flex;
    flex-direction: column;
  }

  .movie-img:hover .overview {
    transform: translateY(0);

  }

  .review {
    position: absolute;
    top: 0;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 40px;
    height: 40px;
    background-color: #c92502;
    color: #fff;
    border-radius: 0 0 16px 0;
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
      0 2px 4px -1px rgba(0, 0, 0, 0.06);
  }

  img {
    display: block;
    width: 100%;
    height: 100%;
  }

  .overview {
    line-height: 1.5;
    position: absolute;
    bottom: 0;
    background-color: rgba(201, 38, 2, 0.9);
    padding: 12px;
    color: #fff;
    transform: translateY(100%);
    transition: 0.3s ease-in-out all;
  }

  .movie-img {
    position: relative;
    overflow: hidden;
  }

  .title {
    margin-top: 8px;
    color: #fff;
    font-size: 20px;
  }

  .release {
    margin-top: 8px;
    color: #c9c9c9;
  }

  .button {
    margin-top: 8px;
  }

  .info {
    margin-top: auto;
  }
</style>