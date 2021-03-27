<template>
  <div id="app">

    <div class="container">

      <div class="grid-container">
        <div class="square" @click="clickSquare(1)" :class="{lighting: isLighting[1]}"></div>
        <div class="square" @click="clickSquare(2)" :class="{lighting: isLighting[2]}"></div>
        <div class="square" @click="clickSquare(3)" :class="{lighting: isLighting[3]}"></div>
        <div class="square" @click="clickSquare(4)" :class="{lighting: isLighting[4]}"></div>
      </div>
      <div class="information_desk">
        <button :disabled="starButtonDisable" @click="gameStart"> Start</button>
        <p class="score">Your score: {{score}}</p>
        <div class="radio-button">
          <input id="easy" type="radio" name="difficult" value="easy" v-model="difficult">
          <label for="easy">Easy</label>
        </div>
        <div class="radio-button">
          <input id="normal" type="radio" name="difficult" value="normal" v-model="difficult">
          <label for="normal">Normal</label>
        </div>
        <div class="radio-button">
          <input id="hard" type="radio" name="difficult" value="hard" v-model="difficult">
          <label for="hard">Hard</label>
        </div>
        <p class="lose-message" v-if="wrongResult">You lose!</p>
      </div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data() {
    return {
      starButtonDisable: false,
      sequenceArray: [],
      difficult: 'easy',
      playerSelect: [],
      sequenceInterval: null,
      isLighting : {
        1: false,
        2: false,
        3: false,
        4: false
      },
      wrongResult: false,
      score: 0,
      isEnable: false,
      sounds: {
        1: require('./assets/sounds/sounds_1.mp3'),
        2: require('./assets/sounds/sounds_2.mp3'),
        3: require('./assets/sounds/sounds_3.mp3'),
        4: require('./assets/sounds/sounds_4.mp3'),
      }
    }
  },
  watch: {
    difficult: function () {
      this.isEnable = false
      this.gameStart();
    }
  },
  methods: {
    gameStart() {
      this.score = 0;
      this.isEnable = false
      this.sequenceArray = [];
      this.wrongResult = false;
      this.starButtonDisable = true;
      this.showSequence()
    },

    showSequence() {
      let currentIndex = 0;
      let speed = this.selectedDifficult();
      this.sequenceArray.push(Math.floor(Math.random() * 4 + 1));
      this.playerSelect = [];
      this.sequenceInterval = setInterval(() => {

        if(currentIndex >= this.sequenceArray.length) {
          clearInterval(this.sequenceInterval);
          this.isEnable = true;
        }
        this.soundClick(this.sequenceArray[currentIndex])
        this.lightSquare(this.sequenceArray[currentIndex]);
        currentIndex++;
      },speed);
    },

    lightSquare(num) {
      this.isLighting[num] = true;
      setTimeout(() => {
        this.isLighting[num] = false;
      }, 300);

    },
    clickSquare(num) {
      if(this.isEnable) {
        this.lightSquare(num);
        this.playerSelect.push(num);
        this.soundClick(num)
        this.checkResult();
      }
    },
    checkResult() {
      for (let i = 0; i < this.playerSelect.length; i++){
        if(this.playerSelect[i] !== this.sequenceArray[i]) {
          this.wrongResult = true;
          this.isEnable = false;
          this.starButtonDisable = false;
          return;
        }
      }
      if(this.playerSelect.length === this.sequenceArray.length) {
        this.score++;
        this.isEnable = false
        this.showSequence();
      }
    },
    selectedDifficult() {
      let speed = 0
      switch (this.difficult){
        case 'easy':
          speed = 1500;
          break;

        case 'normal':
          speed = 1000;
          break;

        case 'hard':
          speed = 400;
          break
      }
      return speed;
    },
    soundClick(num) {
      let audio = new Audio(this.sounds[num]);
      if(this.sounds[num]){
        audio.play();
      } else {
        audio.pause();
      }

    }
  }
}
</script>

<style lang="sass">
*
  box-sizing: border-box
  margin: 0
  padding: 0
  outline: none

#app
  font-family: Avenir, Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #2c3e50
  margin-top: 60px

  .container
    max-width: 1200px
    margin: 40px auto 0 auto
    justify-content: center
    display: flex
    @media (max-width: 600px)
      display: block
    .grid-container
      max-width: 215px
      height: 215px
      background-color: black
      border-radius: 127px
      border: 5px solid black
      overflow: hidden
      margin: 0 auto
      display: grid
      grid-template-columns: 100px 100px
      grid-template-rows: 100px 100px
      grid-gap: 5px

      .square
        cursor: pointer
        background-color: blue

        &:nth-of-type(1)
          background-color: yellow
          opacity: 0.4

          &.lighting
            opacity: 1

        &:nth-of-type(2)
          background-color: blue
          opacity: 0.4

          &.lighting
            opacity: 1

        &:nth-of-type(3)
          background-color: red
          opacity: 0.4

          &.lighting
            opacity: 1

        &:nth-of-type(4)
          background-color: green
          opacity: 0.4

          &.lighting
            opacity: 1
    & .information_desk
      display: block
      text-align: center
      margin: 0 40px
      width: 180px
      & .score
        border: 2px solid blue
        border-radius: 10px
        padding: 2px
        font-weight: 700

      &.lighting
        opacity: 1

      & .radio-button
        margin: 20px 20px
        input
          float: left
        label
          padding-right: 20px

      @media (max-width: 600px)
        margin: 20px auto
        p
          margin: 15px 0

    button
      transition: .4s
      width: 100px
      height: 29px
      background: blue
      color: white
      border: none
      border-radius: 15px

    button:active
      transition: .4s
      background-color: #030366

    p
      margin: 40px 0
  .lose-message
    font-size: 24px
    font-weight: 900
    color: red

</style>
