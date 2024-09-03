<script setup>
import { ref } from 'vue'
import {money} from '@/components/globalVar'

let sumBet = ref(null)
let win = ref(0)
let losses = ref(0)
let lvl = ref(1)
let allClick = ref(0)
let multyplay = ref(lvl.value * 2)
let isStartGame = ref(false)

const localMoney = JSON.parse(localStorage.getItem('localMoney'))
const localWin = JSON.parse(localStorage.getItem('localWin'))
const localLosses = JSON.parse(localStorage.getItem('localLosses'))
const localLvl = JSON.parse(localStorage.getItem('localLvl'))
const localAllClick = JSON.parse(localStorage.getItem('localAllClick'))
const localAlMultyplay = JSON.parse(localStorage.getItem('localAlMultyplay'))
const localAlStep = JSON.parse(localStorage.getItem('localAlStep'))

if (localMoney || localMoney === 0) {
  money.value = localMoney
}
if (localWin || localLosses) {
  win.value = localWin
  losses.value = localLosses
}
if (localLvl) {
  lvl.value = localLvl
  allClick.value = localAllClick
  multyplay.value = localAlMultyplay
}

function rollCoin() {
  if (sumBet.value > 0 && money.value >= sumBet.value && !isStartGame.value) {
    money.value -= sumBet.value
    isStartGame.value = true
    const coin = document.querySelector('.coin')
    let rNum = Math.floor(Math.random() * 2 + 1)

    if (rNum === 1) {
      coin.style.transform = 'rotateX(360deg)'
      setTimeout(() => {
        money.value += sumBet.value * 2
        win.value++
      }, 200)
    } else {
      coin.style.transform = 'rotateX(540deg)'
      setTimeout(() => {
        money.value += 0
        losses.value++
      }, 200)
    }
    setTimeout(() => {
      coin.style.transform = 'rotateX(0deg)'
      localStorage.setItem('localMoney', JSON.stringify(money.value))
      localStorage.setItem('localWin', JSON.stringify(win.value))
      localStorage.setItem('localLosses', JSON.stringify(losses.value))
    }, 2000)
    setTimeout(() => {
      isStartGame.value = false
    }, 3000)
  } else {
    document.querySelector('.error').classList.add('show')
    setTimeout(() => {
      document.querySelector('.error').classList.remove('show')
    }, 1000)
  }
}
let step = 100
if (localAlStep) {
  step = localAlStep
}
function clicker() {
  money.value += multyplay.value
  allClick.value++
  if (allClick.value === step) {
    lvl.value++
    multyplay.value = lvl.value * 2
    step += 100
  }
  localStorage.setItem('localMoney', JSON.stringify(money.value))
  localStorage.setItem('localLvl', JSON.stringify(lvl.value))
  localStorage.setItem('localAllClick', JSON.stringify(allClick.value))
  localStorage.setItem('localAlMultyplay', JSON.stringify(multyplay.value))
  localStorage.setItem('localAlStep', JSON.stringify(step))
}

</script>

<template>
  <div class="coin__flip__block">
    <div class="error"><span class="err"></span>Невозможно начать игру</div>
      <div class="play__block__nav">
        <div class="play__block__nav__left">
          <p class="play__block__nav__left__title">Статистика</p>
          <div class="play__block__nav__left__body">
            <div class="stat">
              <p>Всего сыграно:</p>
              <b>{{ win + losses }}</b>
            </div>
            <div class="stat">
              <p>Побед:</p>
              <b class="w">{{ win }}</b>
            </div>
            <div class="stat">
              <p>Проигрышей</p>
              <b class="l">{{ losses }}</b>
            </div>
            <div class="stat">
              <p>Кликов:</p>
              <b>{{ allClick }}</b>
            </div>
          </div>
        </div>
        <div class="play__block__nav__right">
          <div class="balance">
            <p class="balance__text">Баланс</p>
            <p class="balance__money">{{ money }}р</p>
          </div>
          <div class="statistick">
            <div class="statistick__click" @click="clicker">
              <p>+{{ multyplay }}р</p>
            </div>
            <div class="statistick__lvl">
              <p>{{ lvl }} LVL</p>
            </div>
          </div>
          <input type="number" placeholder="Сумма ставки..." class="money__bat" v-model="sumBet" />
        </div>
      </div>

      <div class="coin" @click="rollCoin">
        <div class="fornt" :class="{ started: isStartGame }">
          <p>COIN</p>
          <p class="multyplay">2x</p>
        </div>
        <div class="back" :class="{ started: isStartGame }">
          <p>FLIP</p>
          <p class="multyplay">0x</p>
        </div>
      </div>
    </div>
</template>

<style scoped>
.coin__flip__block {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  row-gap: 50px;
}
.error {
  position: absolute;
  right: 0;
  bottom: 0;
  background-color: #fff;
  width: 300px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 15px;
  font-weight: bold;
  opacity: 0;
  transition: 0.5s;
}
.error span {
  width: 6px;
  height: 100%;
  position: absolute;
  left: 0;
}
.show {
  opacity: 1;
}
.play__block__nav {
  display: flex;
  column-gap: 20px;
}
.play__block__nav__left {
  background-color: #fff;
  width: 500px;
  border-radius: 10px;
  padding: 20px;
}
.play__block__nav__left__title {
  font-size: 25px;
  font-weight: bold;
  color: rgba(0, 0, 0, 0.459);
  text-align: center;
  margin-bottom: 25px;
}
.play__block__nav__left__body {
  display: flex;
  flex-direction: column;
  row-gap: 10px;
}
.stat {
  display: flex;
  justify-content: space-between;
  font-size: 20px;
}
.w {
  color: #71ab83;
}
.l {
  color: #ab7171;
}
.play__block__nav__right {
  background-color: #fff;
  width: 500px;
  border-radius: 10px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  row-gap: 25px;
}
.balance {
  background-color: #f7f7f7;
  height: 45px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-radius: 10px;
  padding: 15px;
  font-size: 20px;
  font-weight: bold;
}
.balance__text {
  color: rgba(0, 0, 0, 0.288);
}
.statistick {
  display: flex;
  column-gap: 20px;
}

.statistick__click {
  background-color: #f7f7f7;
  width: 30%;
  height: 75px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 10px;
  color: rgba(0, 0, 0, 0.288);
  font-size: 20px;
  font-weight: bold;
  cursor: pointer;
  user-select: none;
}
.statistick__click:hover {
  background-color: #ececec;
}
.statistick__lvl {
  background-color: #f7f7f7;
  width: 100%;
  height: 75px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 10px;
  font-size: 20px;
  font-weight: bold;
}
.money__bat {
  background-color: #f7f7f7;
  border: none;
  padding: 15px 25px;
  border-radius: 10px;
  color: rgb(0, 0, 0);
  font-size: 20px;
  font-weight: bold;
}
.money__bat::placeholder {
  color: rgba(0, 0, 0, 0.164);
  font-size: 20px;
  font-weight: bold;
}
.money__bat:focus {
  outline: rgb(22, 152, 175) 1px solid;
}
.coin {
  width: 250px;
  height: 250px;
  position: relative;
  perspective: 1000px;
  transform-style: preserve-3d;
  transition: transform 1s;
  user-select: none;
  cursor: pointer;
}

.fornt,
.back {
  width: 100%;
  height: 100%;
  position: absolute;
  line-height: 100px;
  color: rgba(0, 0, 0, 0.281);
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 25px;
  border-radius: 50%;
  backface-visibility: hidden;
  background-color: #fff;
  transition: 0.5s;
}

.fornt {
  transform: translateZ(1px);
}

.back {
  transform: rotateX(180deg) translateZ(1px);
}

.coin.one {
  transform: rotateX(360deg);
}
.coin.two {
  transform: rotateX(540deg);
}

.multyplay {
  position: absolute;
  bottom: -20px;
  right: 115px;
  font-size: 17px;
  color: rgba(0, 0, 0, 0.603);
}
.err {
  background-color: #d85353fd;
}
.started {
  background-color: #57ddf5;
}
</style>
