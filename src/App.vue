<template>
  <div id="app">
    <h1>Simon Says</h1>
    <h2>Round: {{ round }}</h2>
    <div class="simon-game">
      <div class="game-circle">
        <div class="blue quarter" @click="quarterClick('blue')"></div>
        <div class="red quarter" @click="quarterClick('red')"></div>
        <div class="yellow quarter" @click="quarterClick('yellow')"></div>
        <div class="green quarter" @click="quarterClick('green')"></div>
        <button @click="startGame" class="start-btn">Start</button>

      </div>
      <div class="select-mode-container">
        <h3>Mode: {{ mode }}</h3>
        <button @click="setMode('easy')" id="easy">Easy</button>
        <button @click="setMode('normal')" id="normal">Normal</button>
        <button @click="setMode('hard')" id="hard">Hard</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      round: 0,
      mode: 'easy',
      sequence: [],
      isPlaying: false,
      speed: {
        easy: 1500,
        normal: 1000,
        hard: 400
      }
    };
  },
  methods: {
    startGame() {
      this.round = 1;
      this.sequence = [];
      this.generateSequence();
      this.playSequence();
    },
    generateSequence() {
      const colors = ['blue', 'red', 'yellow', 'green'];
      for (let i = 0; i < this.round; i++) {
        const randomIndex = Math.floor(Math.random() * colors.length);
        this.sequence.push(colors[randomIndex]);
      }
      console.log(this.sequence)
    },
    playSequence() {
      this.isPlaying = true;
      let index = 0;
      const playNextColor = () => {
        if (index < this.sequence.length) {
          this.activateColor(this.sequence[index], () => {
            index++;
            playNextColor();
          });
        } else {
          this.isPlaying = false;
        }
      };
      playNextColor();
    },
    activateColor(color, callback) {
      const audio = new Audio(require(`./assets/${color}.mp3`));
      audio.play();
      const element = document.querySelector(`.${color}`);
      element.classList.add('active');
      setTimeout(() => {
        element.classList.remove('active');
        if (callback) {
          callback();
        }
      }, this.speed[this.mode]);
    },

    quarterClick(color, callback) {
      if (this.isPlaying) return;
      const expectedColor = this.sequence.shift();
      const audio = new Audio(require(`./assets/${color}.mp3`));
      audio.play();
      const element = document.querySelector(`.${color}`);
      element.classList.add('active');
      setTimeout(() => {
        element.classList.remove('active');
        if (callback) {
          callback();
        }
      }, this.speed[this.mode]);
      console.log(expectedColor)
      if (color === expectedColor) {
        if (this.sequence.length === 0) {
          this.round++;
          setTimeout(() => {
            this.generateSequence();
            this.playSequence();
          }, 2500);
        }
      } else {
        alert('Wrong color! Game over.');
        this.round = 0;
        this.sequence = [];
      }
    },
    setMode(mode) {
      this.mode = mode;
    }
  }
};
</script>

<style lang="sass">
#app
  display: flex
  flex-direction: column
  align-items: center
  height: 100vh
  font-family: "Anta", sans-serif

.active
  filter: brightness(300%)

.simon-game
  display: flex
  align-items: center
  gap: 50px

h1
  margin-top: 80px
  font-size: 50px

h2
  margin-bottom: 80px
  font-size: 35px

h3
  font-size: 30px
  border-bottom: 1px solid

.game-circle
  display: flex
  flex-wrap: wrap
  justify-content: space-between
  width: 500px
  height: 500px
  position: relative

.quarter
  width: 50%
  height: 50%
  cursor: pointer

.start-btn
  position: absolute
  top: 38%
  left: 38%
  border-radius: 100px
  width: 130px
  height: 130px
  background-color: #29282c
  color: white
  font-size: 25px
  font-weight: bold
  border: 8px solid dimgray

.blue
  background-color: #1a8db7
  border-top-left-radius: 300px

.red
  background-color: #a11717
  border-top-right-radius: 300px

.yellow
  background-color: #c0b703
  border-bottom-left-radius: 300px

.green
  background-color: forestgreen
  border-bottom-right-radius: 300px

.select-mode-container
  display: flex
  flex-direction: column
  align-items: center
  height: 500px
  gap: 30px

#easy,
#normal,
#hard
  border-radius: 30px
  width: 100px
  height: 50px
  font-size: 22px
  border-style: none

#easy
  background-color: mediumspringgreen

#normal
  background-color: gold

#hard
  background-color: indianred

button:hover
  cursor: pointer
  filter: brightness(125%)
  transition: filter 0.5s

</style>
