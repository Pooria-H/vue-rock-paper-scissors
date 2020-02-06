<template>
  <div>
    <div class="row">
      <div class="half text-center">
        <hand @handChoosed="onHandChoose" :bus="bus" :which="'you'" :is-game-running="isGameRunning"></hand>
      </div>
      <div class="half text-center">
        <hand @handChoosed="onHandChoose" :bus="bus" :which="'computer'" :is-game-running="isGameRunning"></hand>
      </div>
    </div>
    <div class="text-center u-mT20">
      <template v-if="isGameFinished">
        <div v-html="winnerMessage" style="margin-bottom: 20px;"></div>
        <button @click="newGame" type="button" class="btn">
          New Game
        </button>
      </template>
      <template v-else>
        <button @click="start" :disabled="isGameRunning" type="button" class="btn">
          Start
        </button>
      </template>
    </div>
  </div>
</template>

<script>
import hand from './hand-components/hand';

export default {
  name: 'round',
  props: {
    'bus': {
      type: Object,
      required: true,
    },
    'players': {
      type: Object,
      required: true,
    },
    'winnerOfWholeGame': {
      type: String,
      required: true,
    }
  },
  components: {
    hand,
  },
  computed: {
    looserName() {
      if (this.winner === '') {
        return '';
      }
      else if (this.winner === 'you') {
        return 'computer';
      }
      else {
        return 'you';
      }
    },
    winnerMessage() {
      if (this.winnerOfWholeGame === '') {
        return '';
      }
      else if (this.winnerOfWholeGame === 'you') {
        return '<b>You</b> are the winner!';
      }
      else {
        return '<b>Computer</b> is the winner!';
      }
    }
  },
  methods: {
    start() {
      this.clearRound();
      this.isGameRunning = true;
      this.$emit('gameIsRunning');
      this.bus.$emit('roundStarted');

      setTimeout(() => {
        this.stop();
      }, 2000);
    },
    stop() {
      this.isGameRunning = false;
    },
    onHandChoose(input) {
      this.numberOfReturnedHands += 1;

      if (input[0] === 'you') {
        this.returnedHands.you = input[1];
      } else {
        this.returnedHands.computer = input[1];
      }

      if (this.numberOfReturnedHands === 2) {
        this.checkWhoIsTheWinner(this.returnedHands.you, this.returnedHands.computer);
        this.numberOfReturnedHands = 0;
      }
    },
    checkWhoIsTheWinner(you, computer) {
      if (you === 'rock') {
        if (computer === 'rock') {
          this.winner = '';
        }
        else if (computer === 'paper') {
          this.winner = 'computer';
        }
        else if (computer === 'scissors') {
          this.winner = 'you';
        }
      }
      else if (you === 'paper') {
        if (computer === 'rock') {
          this.winner = 'you';
        }
        else if (computer === 'paper') {
          this.winner = '';
        }
        else if (computer === 'scissors') {
          this.winner = 'computer';
        }
      }
      else if (you === 'scissors') {
        if (computer === 'rock') {
          this.winner = 'computer';
        }
        else if (computer === 'paper') {
          this.winner = 'you';
        }
        else if (computer === 'scissors') {
          this.winner = '';
        }
      }

      this.$emit('roundFinished', this.winner);
      this.bus.$emit('roundFinished', this.looserName);
    },
    clearRound() {
      this.returnedHands.computer = '';
      this.returnedHands.you = '';
      this.winner = '';
    },
    onWeHaveWinner(winner) {
      this.isGameFinished = true;
    },
    newGame() {
      this.isGameFinished = false;
      this.clearRound();
      this.$emit('newGame');
      this.bus.$emit('resetHand');
    }
  },
  mounted() {
    this.bus.$on('weHaveWinner', this.onWeHaveWinner);
  },
  data() {
    return {
      winner: '',
      isGameRunning: false,
      isGameFinished: false,
      returnedHands: {
        you: '',
        computer: '',
      },
      numberOfReturnedHands: 0,
    };
  }
}
</script>
