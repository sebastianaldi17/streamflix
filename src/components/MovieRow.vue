<template>
  <v-container>
    <v-row>
      <v-col cols="12" sm="3">
        <img :src="`${imageUrl}`" class="movieimage" />
      </v-col>
      <v-col cols="12" sm="9">
        <h1>{{ title }}</h1>
        <p>{{ price }}</p>
        <p v-if="purchased">Anda telah membeli film ini.</p>
        <p v-else>Anda belum membeli film ini.</p>
        <a :href="`${generateLink()}`">View details</a>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    purchased: false
  }),
  name: "MovieRow",
  methods: {
    generateLink() {
      let slag = this.title.split(" ").join("-")
      return `${this.id}-${slag}`
    }
  },
  mounted() {
    if(localStorage.getItem(this.id) !== null) {
      this.purchased = true
    }
  },
  props: {
    id: {
      type: String,
    },
    imageUrl: {
      type: String,
    },
    price: {
      type: String,
    },
    title: {
      type: String,
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.movieimage {
  width: 75%;
}
</style>
