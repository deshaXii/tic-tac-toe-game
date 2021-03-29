<template>
  <div>
    <div class="gameStatus" :class="statusColor">
      {{ statusMsg }}
    </div>
    <div class="grid">
      <Cell v-for="n in 9" :marker="n" :key="n" />
    </div>
  </div>
</template>

<script>
import Cell from "./Cell.vue";
import EventBus from "../eventBus";
export default {
  components: { Cell },
  name: "Grid",
  data() {
    return {
      activePlayer: "O",
      gameStatus: "turn",
      statusMsg: "O's Turn",
      statusColor: "statusTurn",
      moves: 0,
      winConditions: [
        [1, 2, 3],
        [4, 5, 6],
        [7, 8, 9], // rows
        [1, 4, 7],
        [2, 5, 8],
        [3, 6, 9], // columns
        [1, 5, 9],
        [3, 5, 7], // diagonals
      ],
      cells: {
        1: "",
        2: "",
        3: "",
        4: "",
        5: "",
        6: "",
        7: "",
        8: "",
        9: "",
      },
    };
  },
  computed: {
    nextActivePlayer() {
      if (this.activePlayer === "O") {
        return "X";
      }
      return "O";
    },
  },
  created() {
    EventBus.$on("shot", (cellNumber) => {
      this.cells[cellNumber] = this.activePlayer;
      this.moves++;
      this.gameStatus = this.changeGameStatus();
    });
    EventBus.$on("resetGrid", () => {
      Object.assign(this.$data, this.$options.data());
    });
  },
  methods: {
    changePlayer() {
      this.activePlayer = this.nextActivePlayer;
      this.statusMsg = `${this.activePlayer}'s Turn`;
    },
    checkForWin() {
      let cells = this.cells;
      return this.winConditions.some((condition) => {
        let count = 0;
        condition.forEach((cell) => {
          if (cells[cell] === this.activePlayer) {
            count++;
          }
        });
        if (count === 3) {
          return true;
        }
        return false;
      });
    },
    gameIsWon() {
      EventBus.$emit("win", this.activePlayer);
      this.statusMsg = `${this.activePlayer} Wins`;
      EventBus.$emit("freeze");
      return "win";
    },
    changeGameStatus() {
      if (this.checkForWin()) {
        return this.gameIsWon();
      } else if (this.moves === 9) {
        return "draw";
      }
      this.changePlayer();
      return "turn";
    },
  },
  watch: {
    gameStatus() {
      if (this.gameStatus === "win") {
        this.statusColor = "statusWin";
      } else if (this.gameStatus === "draw") {
        this.statusColor = "statusDraw";
        this.statusMsg = "There's no winner";
      }
    },
  },
};
</script>

<style>
.grid {
  display: grid;
  grid-template-columns: 120px 120px 120px;
  grid-gap: 10px;
  background-color: #34495e;
  color: white;
  width: 100%;
  position: relative;
  padding: 10px;
  box-sizing: border-box;
}
.gameStatus {
  border-top-right-radius: 50px;
  border-top-left-radius: 50px;
  padding: 20px 0;
  font-size: 2em;
  font-weight: 800;
  color: white;
  text-align: center;
}
.statusTurn {
  background-color: #ffc107;
}
.statusWin {
  background-color: #8bc34a;
}
.statusDraw {
  background-color: #dedede;
}
</style>
