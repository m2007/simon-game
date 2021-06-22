<template>
<h3>Simon The Game</h3>
  <div class="container">
    <div class="field">

      <div class="field__button" :class="{field__button_a: activeNum == 0}"
      @mousedown="userMDown(0)" @mouseup="userMUp(0)"></div>

      <div class="field__button" :class="{field__button_a: activeNum == 1}"
      @mousedown="userMDown(1)" @mouseup="userMUp(1)"></div>
    
      <div class="field__button" :class="{field__button_a: activeNum == 2}"
      @mousedown="userMDown(2)" @mouseup="userMUp(2)"></div>
      
      <div class="field__button" :class="{field__button_a: activeNum == 3}"
      @mousedown="userMDown(3)" @mouseup="userMUp(3)"></div>
      
    </div>

    <div class="panel">
      <p>Раунд {{round}}</p>
      
      <p>Уровень сложности
        <select v-model="diff">
          <option value="1.5">Легкий</option>
          <option value="1">Средний</option>
          <option value="0.4">Сложный</option>
        </select>
      </p>
      <p v-if="loseAlert">Вы проиграли!</p>
      <button @click="init()">Начать</button>
    </div>
  </div>

</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data: () => ({
    sequence: [],
    playing: false,
    loseAlert: false,
    showing: false,
    userIndex: 0,
    diff: "1.5",
    activeNum: null,
    sounds: [],
    activeSound: new Audio()
  }),
  computed: {
    round() {
      return this.sequence.length
    }
  },
  mounted() {
    this.sounds.push(require('./assets/0.mp3'))
    this.sounds.push(require('./assets/1.mp3'))
    this.sounds.push(require('./assets/2.mp3'))
    this.sounds.push(require('./assets/3.mp3'))
  },
  methods: {
    init() {
      this.loseAlert = false
      this.playing = true
      this.userIndex = 0
      this.sequence = []
      this.addRandomButton()
      this.showSequence()
    },
    addRandomButton()  {
      let index = Math.floor(Math.random() * 4)
      this.sequence.push(index)
    },
    userMDown(num) {
      this.activeNum = num
    },
    userMUp(num) {
      this.activeNum = null
      if (!this.playing || this.showing) return
      
      if (num == this.sequence[this.userIndex]) {
        this.userIndex++

        if (this.userIndex >= this.round) {
          this.userIndex = 0
          this.addRandomButton()
          this.showSequence()
        }

      } else {
        this.playing = false
        this.loseAlert = true
      }

      this.playSound(num)
    },
    showSequence() {
      this.showing = true
      let num = 0
      let tid
      let iter = (show) => {
        if (show) {
          if (num >= this.round) {
            clearTimeout(tid)
            this.activeNum = null
            this.showing = false
            return
          }

          this.activeNum = this.sequence[num]
          this.playSound(this.activeNum)
          num++
          tid = setTimeout(iter, (this.diff * 1000) - 200, !show)
        } else {
          this.activeNum = null
          tid = setTimeout(iter, 200, !show)
        }
      }

      tid = setTimeout(() => { iter(false) }, 500)
    },
    playSound(num) {
      this.activeSound.src = this.sounds[num]
      this.activeSound.play()
    }
  }
}
</script>

<style lang="scss">
.container {
  display: flex;
  justify-content: space-around;
  align-items: center;
}
.field {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-auto-rows: 100px;

  &__button {
    opacity: 0.4;
    width: 100px;
    margin: 2px;
    cursor: pointer;
    transition: .1s;

    &_a {
      opacity: 1;
    }

    &:nth-child(1) {
      background: red;
    }

    &:nth-child(2) {
      background: blue;
    }

    &:nth-child(3) {
      background: yellow;
    }

    &:nth-child(4) {
      background: green;
    }
  }
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
