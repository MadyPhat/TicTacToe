<template>
  <div class="tic-tac-toe">
    <h1>Tic Tac Toe</h1>
    <div class="board">
      <div class="row" v-for="(row, rowIndex) of board" :key="rowIndex">
        <div
          class="cell"
          v-for="(cell, cellIndex) of row"
          :key="cellIndex"
          :class="{
            'cell-x': cell === 'X',
            'cell-o': cell === 'O',
            'turn-x': currentPlayer === 'X',
            'turn-o': currentPlayer === 'O',
            'cell-disabled': gameover,
          }"
          :disabled="cell !== null"
          @click="playMove(rowIndex, cellIndex)"
        >
          {{ cell }}
        </div>
      </div>
    </div>
    <div>
      <div v-if="winner">
        Player
        <span
          :class="{
            'player-x': winner === 'X',
            'player-o': winner === 'O',
          }"
        >
          {{ winner }}
        </span>
        has won!
      </div>
      <div v-else-if="isTie">It's a tie!</div>
      <div v-else>
        Player
        <span
          :class="{
            'player-x': currentPlayer === 'X',
            'player-o': currentPlayer === 'O',
          }"
          >{{ currentPlayer }}</span
        >'s turn
      </div>
      <NuxtParticles
        id="tsparticles"
        :options="particleConfig"
        @load="particlesInit"
      ></NuxtParticles>
    </div>
    <button @click="resetGame">Reset Game</button>
  </div>
</template>
<script setup lang="ts">
import type { Container, IOptions } from "@tsparticles/engine";

const winner = ref<string | null>(null);
const isTie = ref(false);
const gameover = ref(false);
const currentPlayer = ref("X");

let board = reactive([
  ["", "", ""],
  ["", "", ""],
  ["", "", ""],
]);

const checkWinner = () => {
  const value = currentPlayer.value;
  for (let i = 0; i < 3; i++) {
    if (board[i].every((cell) => cell === value)) return true;
    if (board.every((row) => row[i] === value)) return true;
  }
  return (
    (board[0][0] === value && board[1][1] === value && board[2][2] === value) ||
    (board[0][2] === value && board[1][1] === value && board[2][0] === value)
  );
};

const checkTie = () => {
  for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
      if (board[i][j] === "") {
        return false;
      }
    }
  }
  return true;
};

const resetGame = () => {
  board = reactive([
    ["", "", ""],
    ["", "", ""],
    ["", "", ""],
  ]);
  winner.value = null;
  isTie.value = false;
  gameover.value = false;
  currentPlayer.value = "X";
  particleContainer.value?.stop();
  particleContainer.value?.refresh();
};

const playMove = (row: number, col: number) => {
  if (board[row][col] === "" && !gameover.value) {
    board[row][col] = currentPlayer.value;
    if (checkWinner()) {
      winner.value = currentPlayer.value;
      gameover.value = true;
      particleContainer.value?.play();
    } else if (checkTie()) {
      isTie.value = true;
      gameover.value = true;
    } else {
      currentPlayer.value = currentPlayer.value === "X" ? "O" : "X";
    }
  }
};
const particleConfig = {
    autoPlay: false,
  fullScreen: {
    zIndex: -1
  },
  emitters: {
    position: {
      x: 50,
      y: 100
    },
    rate: {
      quantity: 5,
      delay: 0.15
    },
  },
  particles: {
    color: {
      value: ["#1E00FF", "#FF0061", "#E1FF00", "#00FF9E"]
    },
    move: {
      decay: 0.05,
      direction: "top",
      enable: true,
      gravity: {
        enable: true
      },
      outModes: {
        top: "none",
        default: "destroy"
      },
      speed: {
        min: 50,
        max: 100
      },
    },
    number: {
      value: 0
    },
    opacity: {
      value: 1
    },
    rotate: {
      value: {
        min: 0,
        max: 360
      },
      direction: "random",
      animation: {
        enable: true,
        speed: 30
      }
    },
    tilt: {
      direction: "random",
      enable: true,
      value: {
        min: 0,
        max: 360
      },
      animation: {
        enable: true,
        speed: 30
      }
    },
    size: {
      value: 3,
      animation: {
        enable: true,
        startValue: "min",
        count: 1,
        speed: 16,
        sync: true
      }
    },
    roll: {
      darken: {
        enable: true,
        value: 25
      },
      enlighten: {
        enable: true,
        value: 25
      },
      enable: true,
      speed: {
        min: 5,
        max: 15
      }
    },
    wobble: {
      distance: 30,
      enable: true,
      speed: {
        min: -7,
        max: 7
      }
    },
    shape: {
      type: ["circle", "square"],
      options: {

      }
    }
  }
};
const particleContainer = ref<Container | null>(null);
const particlesInit = (container: Container) => {
    particleContainer.value = container;
};
</script>
<style scoped lang="scss">
.tic-tac-toe {
  --OColor: #00f;
  --XColor: #f00;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  font-family: Arial, sans-serif;
}
.board {
  padding: 10px;
  border: 1px solid #cccccc4b;
  border-radius: 10px;
  margin: 10px;
}

.row {
  clear: both;
}

.cell {
  width: 50px;
  height: 50px;
  float: left;
  margin-right: -1px;
  margin-bottom: -1px;
  line-height: 50px;
  text-align: center;
  margin: 5px;
  border-radius: 8px;
  background-color: #f2f2f2;
  transition: background-color 0.3s ease;
  animation: pop 0.5s ease;
  cursor: not-allowed;
  font-size: 40px;
  &:hover:not(.cell-x):not(.cell-o):not(.cell-disabled) {
    cursor: pointer;
    &:is(.turn-x) {
      background: color-mix(in srgb, var(--XColor) 10%, transparent);
    }
    &:is(.turn-o) {
      background: color-mix(in srgb, var(--OColor) 10%, transparent);
    }
  }

  @keyframes pop {
    0% {
      transform: scale(1);
    }
    50% {
      transform: scale(0.8);
    }
    100% {
      transform: scale(1);
    }
  }
}

.cell-x {
  color: var(--XColor);
  background: color-mix(in srgb, var(--XColor) 10%, transparent);
}

.cell-o {
  color: var(--OColor);
  background: color-mix(in srgb, var(--OColor) 10%, transparent);
}

.player-x {
  color: var(--XColor);
}
.player-o {
  color: var(--OColor);
}

button {
  margin-top: 20px;
  padding: 5px 10px;
  border-radius: 5px;
  background: #2a7b9b;
  background: linear-gradient(
    90deg,
    color-mix(in srgb, var(--OColor) 10%, transparent) 0%,
    color-mix(in srgb, var(--XColor) 10%, transparent) 100%
  );
  color: black;
  border: none;
  cursor: pointer;
  transition: background 0.5s ease;
  &:hover {
    background: linear-gradient(
      90deg,
      color-mix(in srgb, var(--OColor) 50%, transparent) 0%,
      color-mix(in srgb, var(--XColor) 50%, transparent) 100%
    );
  }
}
</style>
