<template>
  <div id="app">
    <h1>
      Tic Tac Toe Game
    </h1>
    <h2 class="statistics">
      <span class="playerNames">X</span> has
      <span class="result"> {{ wins["X"] }} </span> wins | matche
      <span class="result">{{matches + 1}}</span> |
      <span class="playerNames">O</span> has
      <span class="result"> {{ wins["O"] }} </span> wins
    </h2>
    <Grid />
    <button class="restart" type="button" @click="restart">Restart</button>
  </div>
</template>

<script>
import Grid from "./components/Grid.vue";
import EventBus from "./eventBus";

export default {
  name: "App",
  components: {
    Grid,
  },
  data() {
    return {
      matches: 0,
      wins: {
        O: 0,
        X: 0,
      },
    };
  },
  methods: {
    restart() {
      EventBus.$emit("clearCells");
      EventBus.$emit("resetGrid");
      this.matches++;
    },
  },
  created() {
    EventBus.$on("win", (winner) => {
      this.wins[winner]++;
    });
  },
};
</script>

<style>
@media (max-width: 768px) {
  #app {
    max-width: 70%;
  }
  .grid {
    grid-template-columns: 31% 31% 31%;
  }
  .cell {
    height: 75px;
    line-height: 75px;
  }
}
#app {
  font-family: "Dosis", sans-serif;
  margin: 0 auto;
  max-width: 400px;
  color: #2c3e50;
}
.restart {
  width: 100%;
  margin: 0;
  background-color: #ff5722;
  border: none;
  border-bottom-left-radius: 50px;
  padding: 20px 0;
  color: #fff;
  border-bottom-right-radius: 50px;
  font-size: 2em;
  outline: none;
  cursor: pointer;
  text-transform: capitalize;
}
#app h1 {
  text-transform: uppercase;
  text-align: center;
  font-weight: bold;
  font-size: 3em;
}
.statistics {
  font-weight: 400;
  text-align: center;
}
.statistics .result {
  color: #00bcd4;
  font-weight: 600;
}
.statistics .playerNames {
  text-decoration: underline;
  color: #2c3e50
}
</style>
