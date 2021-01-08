<template>
<div>
      <movie-row v-for="movie in movies" :key="movie.id"
      v-bind:id="movie.id"
      v-bind:imageUrl="movie.poster_path"
      v-bind:price="movie.price"
      v-bind:title="movie.title"
    />
</div>

</template>

<script>
import MovieRow from '@/components/MovieRow.vue'
import {API_KEY} from '../Key'
const axios = require('axios')

export default {
  name: 'App',

  components: {
    MovieRow,
  },

  data: () => ({
    movies: []
  }),

  mounted() {


    axios.get(`https://api.themoviedb.org/3/movie/now_playing?api_key=${API_KEY}&region=ID&page=1`)
    .then((response) => {
      // console.log(response.data.total_pages) Use for pagination
      this.movies = response.data.results
      for(let i = 0; i < this.movies.length; i++) {
        // Preprocess
        this.movies[i].id = this.movies[i].id.toString()

        // Check for movie poster image
        if(this.movies[i].poster_path === null) this.movies[i].poster_path = `https://via.placeholder.com/300x450`
        else this.movies[i].poster_path = `https://image.tmdb.org/t/p/w342/${this.movies[i].poster_path}`

        // Convert rating to price
        let rating = this.movies[i].vote_average
        if(rating < 3) this.movies[i].price = "Rp. 3.500"
        else if(rating < 6) this.movies[i].price = "Rp. 8.250"
        else if(rating < 8) this.movies[i].price = "Rp. 16.350"
        else this.movies[i].price = "Rp. 21.250"
        
        console.log(this.movies[i])
      }
      // Required stuff for home page:
      // Indicator if film is bought
    })
    .catch((error) => {
      console.log("An error occured while trying to load movies that are now playing.")
      console.log(error)
    })
  }
};
</script>
