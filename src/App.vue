<template>
  <div id="app">
    <!-- <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div> -->
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <a href='/' class='mainlink'>
          <h3>StreamFlix</h3>
        </a>
      </div>

      <v-spacer></v-spacer>

      <div class="d-flex align-center">
        <h3>Saldo anda: {{ cashAsString() }}</h3>
        <button v-on:click="resetStorage">Reset</button>
      </div>
    </v-app-bar>
    <v-app>
      <v-main>
        <router-view />
      </v-main>
    </v-app>
  </div>
</template>

<script>
export default {
  data: () => ({
    cash: 100000,
  }),

  methods: {
    cashAsString() {
      let originalString = this.cash.toString();
      let resultString = "";
      for (let i = originalString.length - 1; i >= 0; i--) {
        resultString = originalString[i] + resultString;
        if ((originalString.length - i) % 3 === 0 && i > 0)
          resultString = "." + resultString;
      }
      resultString = "Rp" + resultString;
      return resultString;
    },
    resetStorage() {
      localStorage.clear()
      window.location.reload()
    }
  },
  mounted() {
    console.log("Mouted on App.vue is called")
    if(localStorage.getItem("cash") === null) {
      console.log("Cash value not detected, setting to 100000")
      this.cash = 100000
      localStorage.setItem("cash", 100000)
    } else {
      console.log("Existing cash value detected")
      this.cash = localStorage.getItem("cash")
    }
  }
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  //text-align: center;
  color: #2c3e50;
  background-color: #FBAB7E;
  background-image: linear-gradient(62deg, #FBAB7E 0%, #F7CE68 100%);

}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}

.mainlink {
  color: inherit;
  text-decoration: inherit; 
}
.mainlink:hover {
  color: rgb(59, 121, 255);
}
</style>
