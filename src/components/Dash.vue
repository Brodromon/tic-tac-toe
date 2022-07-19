<template>
  <div class="dash">
    <div v-if="showBoard" class="btn main-menu" @click="mainMenu()">
      Main menu
    </div>
    <div class="playground">
      <div v-if="!showBoard" class="start-game">
        <label for="PLAYER_ONE_NAME">Player one name:</label>
        <input
          placeholder="X"
          v-model="PLAYER_ONE_NAME"
          name="PLAYER_ONE_NAME"
          type="text"
          @focus="
            PLAYER_ONE_NAME === '' || PLAYER_ONE_NAME == 'X'
              ? (PLAYER_ONE_NAME = '')
              : PLAYER_ONE_NAME
          "
          @blur="
            PLAYER_ONE_NAME === '' ? (PLAYER_ONE_NAME = 'X') : PLAYER_ONE_NAME
          "
        />
        <label for="PLAYER_TWO_NAME">Player two name:</label>
        <input
          placeholder="O"
          v-model="PLAYER_TWO_NAME"
          name="PLAYER_TWO_NAME"
          type="text"
          @focus="
            PLAYER_TWO_NAME === '' || PLAYER_TWO_NAME == 'O'
              ? (PLAYER_TWO_NAME = '')
              : PLAYER_TWO_NAME
          "
          @blur="
            PLAYER_TWO_NAME === '' ? (PLAYER_TWO_NAME = 'O') : PLAYER_TWO_NAME
          "
        />
        <button class="btn" @click="startGame()">Start game</button>
      </div>
      <div v-show="showBoard" class="desc" :class="{ finished: !playable }">
        <div class="player-turn" v-if="playable">
          {{ getPlayerTurnName }}
        </div>
        <div class="winner" v-if="msg">{{ msg }}</div>
        <div class="desc__body">
          <div
            v-for="(cell, index) in 9"
            :key="index"
            :data-index="index"
            class="desc__cell"
            @click="handleClick"
          >
            <!-- <div class="mark"></div> -->
          </div>
          <!-- <div class="desc__cell"></div>
          <div class="desc__cell"></div>
          <div class="desc__cell"></div>
          <div class="desc__cell"></div>
          <div class="desc__cell"></div>
          <div class="desc__cell"></div>
          <div class="desc__cell"></div>
          <div class="desc__cell"></div> -->
        </div>
        <button
          v-if="showBoard == true && playable == false"
          @click="restartGame()"
          class="btn restart-game"
        >
          Restart game
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      PLAYER_ONE_NAME: "",
      PLAYER_TWO_NAME: "",
      PLAYER_X_TURN: true,
      PLAYER_X_MARK: "X",
      PLAYER_O_MARK: "O",
      WINNING_COMBINATIONS: [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6],
      ],
      cells: document.getElementsByClassName("desc__cell"),
      msg: "",
      playable: true,
      showBoard: false,
      winCombination: [],
    };
  },
  methods: {
    handleClick(e) {
      const cell = e.target;
      const currentClass = this.PLAYER_X_TURN
        ? this.PLAYER_X_MARK
        : this.PLAYER_O_MARK;

      if (
        !cell.classList.contains(this.PLAYER_X_MARK) &&
        !cell.classList.contains(this.PLAYER_O_MARK) &&
        this.playable
      ) {
        this.drawMark(cell, currentClass);
        if (this.checkWin(currentClass)) {
          this.showWinner(false);
          this.getWinCombination(currentClass);
        } else if (this.allMarked()) {
          this.showWinner(true);
        } else {
          this.changeTurn();
        }
      }
    },
    drawMark(cell, currentClass) {
      cell.classList.add(currentClass);
    },
    changeTurn() {
      this.PLAYER_X_TURN = !this.PLAYER_X_TURN;
    },

    hasMark(cell) {
      return (
        cell.classList.contains(this.PLAYER_X_MARK) ||
        cell.classList.contains(this.PLAYER_O_MARK)
      );
    },

    allMarked() {
      return [...this.cells].every(
        (cell) =>
          cell.classList.contains(this.PLAYER_X_MARK) ||
          cell.classList.contains(this.PLAYER_O_MARK)
      );
    },

    checkWin(currentClass) {
      this.winCombination = [];
      return this.WINNING_COMBINATIONS.some((combination) => {
        return combination.every((index) => {
          return this.cells[index].classList.contains(currentClass);
        });
      });
    },

    showWinner(draw) {
      if (draw) {
        this.msg = `It's a draw!`;
      } else {
        this.msg = `${
          this.PLAYER_X_TURN ? this.PLAYER_ONE_NAME : this.PLAYER_TWO_NAME
        } winns!`;
      }

      this.endGame();
    },

    getWinCombination(currentClass) {
      this.WINNING_COMBINATIONS.some((combination) => {
        return combination.every((index) => {
          if (
            this.cells[index].classList.contains(currentClass) &&
            !this.playable
          ) {
            this.winCombination.push(index);
          }
          return this.cells[index].classList.contains(currentClass);
        });
      });

      if (!this.playable) {
        this.winCombination = [...new Set(this.winCombination)];
        this.winCombination.forEach((i) =>
          this.cells[i].classList.add("checked")
        );
      }
    },

    startGame() {
      if (!this.PLAYER_ONE_NAME) this.PLAYER_ONE_NAME = this.PLAYER_X_MARK;
      if (!this.PLAYER_TWO_NAME) this.PLAYER_TWO_NAME = this.PLAYER_O_MARK;
      this.playable = true;
      this.showBoard = true;
    },

    endGame() {
      this.playable = false;
    },

    restartGame() {
      this.winCombination = [];
      [...this.cells].forEach((cell) => {
        if (cell.classList.contains(this.PLAYER_X_MARK)) {
          cell.classList.remove(this.PLAYER_X_MARK);
        } else if (cell.classList.contains(this.PLAYER_O_MARK)) {
          cell.classList.remove(this.PLAYER_O_MARK);
        }
        if (cell.classList.contains("checked")) {
          cell.classList.remove("checked");
        }

        return cell;
      });

      this.msg = "";
      this.playable = true;
    },

    mainMenu() {
      this.restartGame();
      this.endGame();
      this.PLAYER_ONE_NAME = "";
      this.PLAYER_TWO_NAME = "";
      this.showBoard = false;
    },
  },

  computed: {
    getPlayerTurnName() {
      if (this.PLAYER_X_TURN) {
        if (this.PLAYER_ONE_NAME !== "X") {
          return `${this.PLAYER_ONE_NAME}'s turn!`;
        } else {
          return `Player X turn!`;
        }
      } else {
        if (this.PLAYER_TWO_NAME !== "O") {
          return `${this.PLAYER_TWO_NAME}'s turn!`;
        } else {
          return `Player O turn!`;
        }
      }
    },
  },
};
</script>

<style lang="scss" scoped>
@import url("https://fonts.googleapis.com/css2?family=Open+Sans:wght@300;400;600&display=swap");

* {
  font-family: "Open Sans", sans-serif;
}
.btn {
  border: none;
  border-radius: 20px;
  font-size: 0.9em;
  color: #fff;
  padding: 8px 30px;
  background: rgb(69, 106, 193);
  font-weight: 600;
  text-align: center;
  &:hover {
    background: rgb(61, 94, 172);
    cursor: pointer;
  }
}
.dash {
  user-select: none;
  background: #fff;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  .main-menu {
    position: absolute;
    left: 10px;
    top: 10px;
  }
  .playground {
    .start-game {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      label {
        width: 100%;
        margin-bottom: 4px;
        font-size: 0.75em;
        font-weight: 600;
      }
      input {
        margin-bottom: 20px;
        border: none;
        border: 1px #aaa solid;
        border-radius: 10px;
        font-size: 0.9em;
        padding: 7px 15px;
      }
    }
    .desc {
      display: flex;
      flex-direction: column;
      position: relative;
      &:not(.finished) {
        .desc__cell {
          &:hover {
            background-color: #c9c9c9;
            cursor: pointer;
          }
          &.X {
            &:hover {
              background-color: #dbdbdb;
              cursor: unset;
            }
          }
          &.O {
            &:hover {
              background-color: #dbdbdb;
              cursor: unset;
            }
          }
        }
      }
      &__body {
        display: flex;
        flex-wrap: wrap;
        width: 210px;
      }
      &__cell {
        height: 60px;
        width: 60px;
        margin: 5px;
        background-color: #dbdbdb;
        background-size: contain;
        background-size: 50px 50px;
        background-repeat: no-repeat;
        background-position: center;
        border-radius: 5px;
        box-sizing: border-box;
        &.X {
          background-image: url("@/assets/X.png");
        }
        &.O {
          background-image: url("@/assets/O.png");
          background-size: 40px 40px;
        }
        &.checked {
          border: 1px rgb(109, 255, 101) solid;
        }
      }

      .winner,
      .player-turn {
        position: absolute;
        top: -40px;
        text-align: center;
        width: 100%;
        font-weight: 600;
        font-size: 1.1em;
      }
      .restart-game {
        position: absolute;
        width: 100%;
        bottom: -40px;
      }
    }
  }
}
</style>