<template>
  <v-container>
    <v-row>
      <v-col cols="12" sm="3">
        <img :src="`${poster_src}`" class="movieimage" />
      </v-col>
      <v-col cols="12" sm="9">
        <p class="text-h4">{{ title }}</p>
        <p class="text-body-1">Sinopsis:</p>
        <p class="text-body-1">{{ desc }}</p>
        <p class="text-body-1">Durasi: {{ duration }} menit</p>
        <p class="text-body-1">Ulasan: {{ rating }} dari 10</p>
        <p class="text-body-1">Harga: {{ priceAsString() }}</p>
        <p class="text-body-1" v-if="!purchased">Anda belum memiliki film ini.</p>
        <p class="text-body-1" v-else>Anda telah membeli film ini.</p>
        <v-btn class="primary" v-on:click="purchaseFilm">Beli film</v-btn>
      </v-col>
    </v-row>

    <v-row>
      <p class="text-h5">Pemeran:</p>
      <v-slide-group show-arrows>
        <v-slide-item v-for="actor in cast" :key="actor.cast_id">
          <v-card class="mx-2" elevation="2" width="138">
            <img :src="`${actor.profile_path}`" maxwidth="138" />
            <p>{{ actor.name }}</p>
          </v-card>
        </v-slide-item>
      </v-slide-group>
    </v-row>

    <v-row>
      <p class="text-h5">Film serupa:</p>
      <v-slide-group show-arrows>
        <v-slide-item v-for="movie in similar" :key="movie.id">
          <v-card class="mx-2" elevation="2" width="300">
            <v-img :src="`${movie.poster_path}`" max-width="300"/>
            <v-card-title>{{ movie.title }}</v-card-title>
          </v-card>
        </v-slide-item>
      </v-slide-group>
    </v-row>

    <v-row>
      <p class="text-h5">Rekomendasi:</p>
      <v-slide-group show-arrows>
        <v-slide-item v-for="movie in recommended" :key="movie.id">
          <v-card class="mx-2" elevation="2" width="300">
            <v-img :src="`${movie.poster_path}`" max-width="300"/>
            <v-card-title>{{ movie.title }}</v-card-title>
          </v-card>
        </v-slide-item>
      </v-slide-group>
    </v-row>
  </v-container>
</template>

<script>
const axios = require("axios");
import {API_KEY} from '../Key'

export default {
  data: () => ({
    // Needed infos:
    // Indicator owned/not
    title: "",
    desc: "",
    poster_src: "",
    rating: 0,
    cast: [],
    duration: 0,
    price: 0,
    similar: [],
    recommended: [],
    movie_id: 0,
    purchased: false
  }),
  methods: {
    priceAsString() {
      let originalString = this.price.toString();
      let resultString = "";
      for (let i = originalString.length - 1; i >= 0; i--) {
        resultString = originalString[i] + resultString;
        if ((originalString.length - i) % 3 === 0 && i > 0)
          resultString = "." + resultString;
      }
      resultString = "Rp" + resultString;
      return resultString;
    },

    purchaseFilm() {
      if(this.purchased) {
        alert("Anda sudah memiliki film ini!")
        return
      }
      let currentBalance = localStorage.getItem("cash")
      if(this.price > currentBalance) {
        alert("Maaf, uang anda tidak cukup!")
      } else {
        localStorage.setItem("cash", currentBalance - this.price)
        // Do something to mark film as purchased
        localStorage.setItem(this.movie_id, true)
        alert("Pembelian film berhasil!")
        window.location.reload()
      }
    }
  },
  mounted() {
    let params = this.$route.params.movieurl.split("-");
    if (params.length <= 0) {
      window.location.replace("/");
    }

    let id = params[0];
    this.movie_id = id
    let limit = 10; // Max number of recommended/similar movies that are displayed

    // Get movie details
    axios
      .get(
        `https://api.themoviedb.org/3/movie/${id}?api_key=${API_KEY}&region=ID`
      )
      .then((response) => {
        let detail = response.data;

        if (params.length !== detail.title.split(" ").length + 1) {
          // Incomplete slag (optional)
          // Redirect to slag path
          let slag = detail.title.split(" ").join("-");
          window.location.replace(`/${id}-${slag}`);
        }

        this.title = detail.title;

        if (detail.overview === "") {
          this.desc = "This movie has no description.";
        } else {
          this.desc = detail.overview;
        }

        if (detail.poster_path === null) {
          this.poster_src = `https://via.placeholder.com/300x450`;
        } else {
          this.poster_src = `https://image.tmdb.org/t/p/w342/${detail.poster_path}`;
        }

        if (detail.runtime === null) {
          this.duration = 0;
        } else {
          this.duration = detail.runtime;
        }

        this.rating = detail.vote_average;
        // Convert rating to price
        let rating = this.rating;
        if (rating < 3) this.price = 3500;
        else if (rating < 6) this.price = 8250;
        else if (rating < 8) this.price = 16350;
        else this.price = 21250;

        // Check if purchased before
        if(localStorage.getItem(detail.id) !== null) {
          this.purchased = true
        }
      })
      .catch((error) => {
        console.log(error);
        window.location.replace("/");
      });

    // Get cast details
    axios
      .get(
        `https://api.themoviedb.org/3/movie/${id}/credits?api_key=${API_KEY}&region=ID`
      )
      .then((response) => {
        this.cast = response.data.cast;
        for (let i = 0; i < this.cast.length; i++) {
          if (this.cast[i].profile_path === null) {
            this.cast[i].profile_path = `https://via.placeholder.com/138x175`;
          } else {
            this.cast[
              i
            ].profile_path = `https://www.themoviedb.org/t/p/w138_and_h175_face${this.cast[i].profile_path}`;
          }
        }
      })
      .catch((error) => {
        console.log("A problem occured while trying to fetch cast information");
        console.log(error);
      });

    // Get similar movies
    axios
      .get(
        `https://api.themoviedb.org/3/movie/${id}/similar?api_key=${API_KEY}&region=ID`
      )
      .then((response) => {
        this.similar = response.data.results;
        this.similar = this.similar.slice(0, limit)
        console.log(this.similar);
        for(let i = 0; i < this.similar.length; i++) {
          if (this.similar[i].poster_path === null) {
            this.similar[i].poster_path = `https://via.placeholder.com/300x450`;
          } else {
            this.similar[i].poster_path = `https://image.tmdb.org/t/p/w342/${this.similar[i].poster_path}`;
          }
        }
      })
      .catch((error) => {
        console.log("A problem occured while trying to fetch similar movies");
        console.log(error);
      });

      // Get recommended movies
      axios
      .get(
        `https://api.themoviedb.org/3/movie/${id}/recommendations?api_key=${API_KEY}&region=ID`
      )
      .then((response) => {
        this.recommended = response.data.results;
        this.recommended = this.recommended.slice(0, limit)
        console.log(this.recommended);
        for(let i = 0; i < this.similar.length; i++) {
          if (this.recommended[i].poster_path === null) {
            this.recommended[i].poster_path = `https://via.placeholder.com/300x450`;
          } else {
            this.recommended[i].poster_path = `https://image.tmdb.org/t/p/w342/${this.recommended[i].poster_path}`;
          }
        }
      })
      .catch((error) => {
        console.log("A problem occured while trying to fetch recommendations");
        console.log(error);
      });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.movieimage {
  width: 75%;
}
.row {
  margin: 1rem;
}
</style>