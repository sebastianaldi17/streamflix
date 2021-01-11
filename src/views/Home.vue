<template>
<div>
    <movie-row v-for="movie in movies" :key="movie.id"
    v-bind:id="movie.id"
    v-bind:imageUrl="movie.poster_path"
    v-bind:price="movie.price"
    v-bind:title="movie.title"
    v-bind:desc="movie.overview"
    />
    <div class="text-center">
      <v-container>
        <v-row justify="center">
          <v-col cols="8">
              <v-pagination
              v-model="current_page"
              :length="pages"
              @input="changePage">
              </v-pagination>
          </v-col>
        </v-row>
      </v-container>
    </div>
</div>

</template>

<script>
import MovieRow from "@/components/MovieRow.vue";
import { API_KEY } from "../Key";
const axios = require("axios");

export default {
  name: "App",

  components: {
    MovieRow,
  },

  data: () => ({
    current_page: 1,
    movies: [],
    pages: 1,
  }),

  methods: {
    changePage(page) {
      window.location.href = window.location.href.split('?')[0] + `?page=${page}`
    }
  },

  mounted() {
    let url = new URL(window.location.href)
    let page_from_url = url.searchParams.get("page")
    if(page_from_url === null) {
      this.current_page = 1
    } else {
      this.current_page = parseInt(page_from_url)
    }
    axios
      .get(
        `https://api.themoviedb.org/3/movie/now_playing?api_key=${API_KEY}&page=${this.current_page}&region=ID`
      ) // remove &region=ID to test pagination (there will be more than one pages)
      .then((response) => {
        this.pages = response.data.total_pages;
        this.movies = response.data.results;
        for (let i = 0; i < this.movies.length; i++) {
          // Preprocess
          this.movies[i].id = this.movies[i].id.toString();

          // Check for movie poster image
          if (this.movies[i].poster_path === null) {
            this.movies[i].poster_path = `https://via.placeholder.com/300x450`;
          }
          else {
            this.movies[i].poster_path = `https://image.tmdb.org/t/p/w342/${this.movies[i].poster_path}`;
          }

          // Add description if null
          if (this.movies[i].overview === "") {
            this.movies[i].overview = "This movie has no description.";
          }

          // Convert rating to price
          let rating = this.movies[i].vote_average;
          if (rating < 3) this.movies[i].price = 3500;
          else if (rating < 6) this.movies[i].price = 8250;
          else if (rating < 8) this.movies[i].price = 16350;
          else this.movies[i].price = 21250;

          // console.log(this.movies[i]);
        }
      })
      .catch((error) => {
        console.log(
          "An error occured while trying to load movies that are now playing."
        );
        console.log(error);
      });
  },
};
</script>
