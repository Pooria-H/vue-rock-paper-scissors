<template>
  <div id="app" class="grid">
    <h1 class="text-center">
      Rock, Paper, Scissors
    </h1>
    <div class="stats row text-center">
      <div class="stats-playedGames">
        Played Games: <b>{{ playedGames }}</b>
      </div>
      <div class="half">
        <h2>You</h2> {{ you }}
      </div>
      <div class="half">
        <h2>Computer</h2> {{ computer }}
      </div>
    </div>

    <round @newGame="onNewGame" @roundFinished="onRoundFinished" :bus="bus" :players="players" :winner-of-whole-game="winner"></round>
  </div>
</template>

<script>
import Vue from 'vue';
import round from './components/round';

export default {
  name: 'app',
  components: {
    round,
  },
  computed: {
    players() {
      return { you: this.you, computer: this.computer };
    },
  },
  watch: {
    winner(newVal, oldVal) {
      if (newVal.length !== 0) {
        this.bus.$emit('weHaveWinner', this.winner);
      }
    },
  },
  methods: {
    onRoundFinished(winner) {
      if (winner.length !== 0) {
        if (winner === 'you') {
          this.you += 1;
        } else {
          this.computer += 1;
        }
      }

      this.playedGames += 1;
      this.checkIfGameHasWinner();
    },
    checkIfGameHasWinner() {
      if (this.you === 3) {
        this.winner = 'you';
      } else if (this.computer === 3) {
        this.winner = 'computer';
      }
    },
    onNewGame() {
      this.playedGames = 0;
      this.you = 0;
      this.computer = 0;
      this.winner = '';
    },
  },
  data() {
    return {
      bus: new Vue(),
      you: 0,
      computer: 0,
      playedGames: 0,
      winner: '',
    };
  }
}
</script>

<style lang="scss">
  body {
    margin: 0;
    padding: 30px;
    font-family: 'Courier New', Courier, monospace;
    font-size: 14px;
    background-color: whitesmoke;
    color: #2e2e2e;
  }

  h1, h2, h3 {
    margin: 0;
  }

  h1 {
    color: white;
    background-color: black;
    padding: 5px 20px;
  }

  .grid {
    max-width: 800px;
    margin: auto;
  }

  .row {
    overflow: hidden;
    clear: both;
  }

  .half {
    float: left;
    width: 50%;
  }

  .stats {
    background-color: #2e2e2e;
    padding: 10px 30px;
    color: white;
  }

  .stats-playedGames {
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid black;
  }

  .text-center {
    text-align: center;
  }

  .hand {
    margin-top: 20px;
    height: 150px;
    transition: all .5s ease;
  }

  .hand-looser {
    transform: scale(.7);
    filter: grayscale(1);
  }

  .handImg {
    display: block;
    margin: auto;
  }

  .handImg-you {
    transform: rotate(45deg);
  }

  .handImg-computer {
    transform: rotateX(180deg) rotate(-135deg);
  }

  .u-mT20 {
    margin-top: 20px;
  }

  .btn {
    background-color: #ba5e00;
    padding: 5px 10px;
    font-size: 1.3em;
    border: 1px solid #a5a5a5;
    border-radius: 3px;
    color: white;
    font-weight: 500;
    cursor: pointer;
    text-transform: uppercase;
    &:disabled {
      background-color: #adadad;
      cursor: initial;
    }
  }

  .lds-ellipsis {
    display: inline-block;
    position: relative;
    width: 64px;
    height: 64px;
  }
  .lds-ellipsis div {
    position: absolute;
    top: 27px;
    width: 11px;
    height: 11px;
    border-radius: 50%;
    background: #000;
    animation-timing-function: cubic-bezier(0, 1, 1, 0);
  }
  .lds-ellipsis div:nth-child(1) {
    left: 6px;
    animation: lds-ellipsis1 0.6s infinite;
  }
  .lds-ellipsis div:nth-child(2) {
    left: 6px;
    animation: lds-ellipsis2 0.6s infinite;
  }
  .lds-ellipsis div:nth-child(3) {
    left: 26px;
    animation: lds-ellipsis2 0.6s infinite;
  }
  .lds-ellipsis div:nth-child(4) {
    left: 45px;
    animation: lds-ellipsis3 0.6s infinite;
  }
  @keyframes lds-ellipsis1 {
    0% {
      transform: scale(0);
    }
    100% {
      transform: scale(1);
    }
  }
  @keyframes lds-ellipsis3 {
    0% {
      transform: scale(1);
    }
    100% {
      transform: scale(0);
    }
  }
  @keyframes lds-ellipsis2 {
    0% {
      transform: translate(0, 0);
    }
    100% {
      transform: translate(19px, 0);
    }
  }
</style>
