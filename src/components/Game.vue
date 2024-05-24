<script setup lang="ts">
import { ref } from 'vue';

const board = ref([
  ['', '', ''],
  ['', '', ''],
  ['', '', '']
]);

const playerXisNext = ref(true);
const itsYourTurn = ref(`Din tur`);
const winner = ref<string | null>(null);
const itsATie = ref(false);
const playerOne = ref('');
const playerTwo = ref('');
const gameStarted = ref(false);
const gameOverText = ref('');

const calculateWinner = (squares: string[][]): string | null => {
  const squareArray = squares.flat();
  
  const combinations: number[][] = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];

  for (let i = 0; i < combinations.length; i++) {
    const [a, b, c] = combinations[i];
    if (squareArray[a] &&  squareArray[a] === squareArray[b] &&  squareArray[a] === squareArray[c]) {
      return squareArray[a];
    }
  }
  return null;
};




const controlIfTie = (squares: string[][]): boolean => {
  for (let row of squares) {
    if (row.includes('')) {
      return false;
    }
  }
  return true;
};


const makeMove = (row: number, col: number) => {
  if (!board.value[row][col] && !winner.value) {
    board.value[row][col] = playerXisNext.value ? 'X' : 'O';
  
    playerXisNext.value = !playerXisNext.value;
    itsYourTurn.value = `Din tur ${playerXisNext.value ? playerOne.value : playerTwo.value}`;

    const winnerSymbol = calculateWinner(board.value);
    if (winnerSymbol) {
      winner.value = winnerSymbol === 'X' ? playerOne.value : playerTwo.value;
      gameOverText.value = `Vinnaren är: ${winner.value}`;
    } else if (controlIfTie(board.value)) {
      itsATie.value = true;
      gameOverText.value = "Det blev lika";
    }
  }
};

const resetGame = () => {
  board.value = [
    ['', '', ''],
    ['', '', ''],
    ['', '', '']
  ];
  playerXisNext.value = true;
  winner.value = null;
  itsATie.value = false;
  gameStarted.value = false;
  itsYourTurn.value = `Din tur: ${playerOne.value}`;
  gameOverText.value = '';
};

const startGame = () => {
  if (playerOne.value && playerTwo.value) {
    gameStarted.value = true;
    itsYourTurn.value = `Din tur: ${playerOne.value}`;
  }
};
</script>

<template>
  <div class="game-container">
    <h1>Tre i rad</h1>
    <section v-if="!gameStarted" class="game-board">
      <div class="player-input">
        <label>Spelare 1:</label>
        <input id="playerOneInput" v-model="playerOne" />
      </div>
      <div class="player-input">
        <label>Spelare 2:</label>
        <input id="playerTwoInput" v-model="playerTwo" />
      </div>
      <button @click="startGame">Starta spelet</button>
    </section>
    <div v-else class="game-play">
      <p class="players-turn">{{ itsYourTurn }}</p>
      <div class="board">
        <div class="row" v-for="(row, rowIndex) in board" :key="rowIndex">
          <div class="cell" v-for="(cell, cellIndex) in row" :key="cellIndex"
            :class="{ 'cell-x': cell === 'X', 'cell-o': cell === 'O' }"
            @click="makeMove(rowIndex, cellIndex)">
            {{ cell }}
          </div>
        </div>
      </div>
      <div class="winning-info">
        <p v-if="winner" class="winning-text">{{ gameOverText }}</p>
        <p v-else-if="itsATie" class="winning-text">{{ gameOverText }}</p>
        <button @click="resetGame" class="reset-button">Starta om spelet</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.game-container {
  text-align: center;
  margin-top: 50px;
}

.game-board {
  margin-bottom: 20px;
}

.player-input {
  margin-bottom: 10px;
}

.board {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  grid-gap: 10px;
  justify-content: center;
}

.row {
  display: contents;
}

.cell {
  width: 100px;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #fff;
  border: 1px solid #000;
  font-size: 2em;
  transition: background-color 0.3s;
}

.cell:hover {
  background-color: #f0f0f0;
}

.cell-x {
  color: rgb(29, 73, 29);
}

.cell-o {
  color: rgb(183, 183, 255);
}

.players-turn {
  font-size: 1.5em;
  margin-bottom: 20px;
}

.winning-info {
  margin-top: 20px;
}

.winning-text {
  font-size: 1.5em;
  margin-top: 10px;
}

.reset-button {
  margin: 10px;
  padding: 5px 10px;
  border: none;
  background-color: #007bff;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s;
}

.reset-button:hover {
  background-color: #0056b3;
}
</style>