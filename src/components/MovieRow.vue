<template>
  <v-sheet style="margin: 2rem">
    <v-container>
      <v-row>
        <v-col cols="12" sm="3">
          <img :src="`${imageUrl}`" class="movieimage" />
        </v-col>
        <v-col cols="12" sm="9">
          <p class="text-h4">{{ title }}</p>
          <p class="text-h6">{{ priceAsString() }}</p>
          <p class="text-body-1">{{ desc }}</p>
          <v-chip style="display: block; width: max-content; margin:8px" v-if="purchased" color="green">
            Anda telah membeli film ini.
          </v-chip>
          <v-chip style="display: block; width: max-content; margin:8px" v-else color="red">
            Anda belum membeli film ini.
          </v-chip>
          <v-btn class="primary" v-on:click="purchaseFilm">Beli film</v-btn>
          <a :href="`${generateLink()}`"><br/>View details</a>
        </v-col>
      </v-row>
    </v-container>
  </v-sheet>
</template>

<script>
export default {
  data: () => ({
    purchased: false,
  }),
  name: "MovieRow",
  methods: {
    generateLink() {
      let slag = this.title.split(" ").join("-");
      return `${this.id}-${slag}`;
    },
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
        localStorage.setItem(this.id, true)
        alert("Pembelian film berhasil!")
        window.location.reload()
      }
    },
  },
  mounted() {
    if (localStorage.getItem(this.id) !== null) {
      this.purchased = true;
    }
  },
  props: {
    desc: {
      type: String
    },
    id: {
      type: String,
    },
    imageUrl: {
      type: String,
    },
    price: {
      type: Number,
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
