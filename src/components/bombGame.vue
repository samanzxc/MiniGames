<script setup>
import { ref } from 'vue'
import { money } from '@/components/globalVar'

let plate = ref([])
let bombCount = ref(2)
let isStarted = ref(false)
let sumBet = ref(null)
let allGame = ref(0)
let winGame = ref(0)
let losseGame = ref(0)

function createPlate() {
  if (sumBet.value <= money.value && sumBet.value && isStarted.value == false) {
    plate.value = []

    for (let i = 1; i < 37; i++) {
      const bomb = plate.value.filter((el) => el.isBomb)
      if (bomb.length < bombCount.value) {
        plate.value.push({ id: i, isBomb: true, activated: false })
      } else {
        plate.value.push({ id: i, isBomb: false, activated: false })
      }
    }
    plate.value.sort(() => Math.random() - 0.5)
    isStarted.value = true
    money.value -= sumBet.value
    localStorage.setItem('localMoney', JSON.stringify(money.value))
  } else {
    document.querySelector('.error__two').classList.add('show')
    setTimeout(() => {
      document.querySelector('.error__two').classList.remove('show')
    }, 1000)
  }
}

let winBet = ref(0)

function checkBomb(bomb) {
  if (isStarted.value) {
    if (bomb.isBomb) {
      bomb.activated = true
      winBet.value = 0
      isStarted.value = false
      plate.value.forEach((el)=>{
    if (el.isBomb) {
      el.activated = true
    }
  })
    } else {
      bomb.activated = true
      winBet.value += sumBet.value * (bombCount.value / 10)
    }
  } else {
    document.querySelector('.error__two').classList.add('show')
    setTimeout(() => {
      document.querySelector('.error__two').classList.remove('show')
    }, 1000)
  }
}
function endGame() {
  isStarted.value = false
  plate.value.forEach((el)=>{
    if (el.isBomb) {
      el.activated = true
    }
  })
  money.value += Number(winBet.value.toFixed(2))
  money.value = Number(money.value.toFixed(2))
  winBet.value = 0
  localStorage.setItem('localMoney', JSON.stringify(money.value))
}
</script>

<template>
  <div class="game__block">
    <div class="error__two"><span></span>Невозможно начать игру</div>
    <div class="game__block__left">
      <div class="game__block__left__plate">
        <div class="bg" v-if="!isStarted"><img src="https://cdn3.iconfinder.com/data/icons/video-games-3/64/14_explosive_bomb-1024.png" alt="">
        <h1>BombGame</h1></div>
        <div class="bomb" v-for="(bomb, index) in plate"
          :key="index"
          @click="checkBomb(bomb)"
          :class="{active__bomb: bomb.isBomb && bomb.activated, no__active__bomb: bomb.activated && !bomb.isBomb}"
        ><img src="https://cdn3.iconfinder.com/data/icons/video-games-3/64/14_explosive_bomb-1024.png" alt=""></div>
      </div>
    </div>
    <div class="game__block__right">
      <div class="game__block__right__info">
        <div class="info__text">
          <img src="/src/assets/img/info.png" alt="info" />
          <p>Как играть?</p>
        </div>
        <p class="game__block__right__info__desc">
          Выберите количество бомбочек, после чего введите сумму ставки
        </p>
        <p class="desc__two">Выбранное кол-во и их умножения:</p>
        <div class="multiply">
          <p>2х - Умножит ставку на 0.2х</p>
          <p>4х - Умножит ставку на 0.4х</p>
          <p>8х - Умножит ставку на 0.8х</p>
          <p>16х - Умножит ставку на 1.6х</p>
        </div>
      </div>
      <div class="game__block__right__settings">
        <div class="game__block__left__money">
          <p>Баланс:</p>
          <b>{{ money }}р</b>
        </div>
        <div class="game__block__right__settings__games">
          <div class="games">
            Сыграно игр:
            <p>{{ allGame }}</p>
          </div>
          <div class="games">
            W
            <p>{{ winGame }}</p>
          </div>
          <div class="games">
            L
            <p>{{ losseGame }}</p>
          </div>
        </div>
        <div class="game__block__right__settings__bomb">
          <p>Количество бомб:</p>
          <div class="multiply__bomb">
            <div
              class="bomb__mult"
              :class="{ active: bombCount == 2 }"
              @click="!isStarted ? (bombCount = 2) : false"
            >
              x2
            </div>
            <div
              class="bomb__mult"
              :class="{ active: bombCount == 4 }"
              @click="!isStarted ? (bombCount = 4) : false"
            >
              x4
            </div>
            <div
              class="bomb__mult"
              :class="{ active: bombCount == 8 }"
              @click="!isStarted ? (bombCount = 8) : false"
            >
              x8
            </div>
            <div
              class="bomb__mult"
              :class="{ active: bombCount == 16 }"
              @click="!isStarted ? (bombCount = 16) : false"
            >
              x16
            </div>
          </div>
        </div>
        <div class="bet">
          <input
            type="number"
            placeholder="Сумма ставки"
            v-model="sumBet"
            :style="[isStarted ? { 'pointer-events': 'none' } : {}]"
          />
        </div>
        <button class="btn__start" v-if="!isStarted" @click="createPlate">Начать играть</button>
        <button class="btn__start" v-if="isStarted" @click="endGame">
          Забрать {{ winBet.toFixed(2) }}
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.game__block {
  display: flex;
  justify-content: center;
  column-gap: 50px;
}
.game__block__left {
  display: flex;
  flex-direction: column;
  align-items: end;
  position: relative;
}
.bg {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background-color: #f8f8f849;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  row-gap: 20px;
  align-items: center;
  justify-content: center;
  color: #00000073;
  z-index: 10;
}
.bg img{
  width: 150px;
  opacity: 0.5;
}
.game__block__left__money {
  display: flex;
  align-items: center;
  column-gap: 25px;
  justify-content: space-between;
  background-color: #f8f8f8;
  padding: 10px;
  border-radius: 10px;
}
.game__block__left__money p {
  color: rgba(0, 0, 0, 0.37);
  font-weight: bold;
}
.game__block__left__plate {
  width: 855px;
  height: 855px;
  background-color: #fff;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  gap: 45px;
  flex-wrap: wrap;
  padding: 45px;
}
.bomb {
  width: 90px;
  height: 90px;
  background-color: #efefef;
  border-radius: 10px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}
.bomb:hover {
  background-color: #d3d3d3;
}
.bomb img{
  width: 30px;
  opacity: .2;
  rotate: 40deg;
}
.game__block__right {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.game__block__right__info {
  background-color: #fff;
  width: 300px;
  padding: 25px;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  row-gap: 15px;
}
.info__text {
  display: flex;
  align-items: center;
  column-gap: 10px;
  color: rgba(0, 0, 0, 0.68);
  font-size: 20px;
  font-weight: bold;
}
.game__block__right__info__desc {
  color: rgba(0, 0, 0, 0.48);
  font-size: 18px;
}
.desc__two {
  color: rgba(0, 0, 0, 0.48);
  font-size: 16px;
}
.multiply {
  color: rgba(0, 0, 0, 0.336);
  font-size: 17px;
  font-weight: bold;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  row-gap: 15px;
}
.game__block__right__settings {
  background-color: #fff;
  width: 300px;
  padding: 15px;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  row-gap: 15px;
  font-size: 20px;
}
.game__block__right__settings__games {
  background-color: #f8f8f8;
  display: flex;
  flex-direction: column;
  row-gap: 10px;
  padding: 10px;
  border-radius: 10px;
  font-size: 20px;
  font-weight: bold;
}
.games {
  display: flex;
  justify-content: space-between;
}
.game__block__right__settings__bomb {
  background-color: #f8f8f8;
  padding: 10px;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  row-gap: 15px;
}
.multiply__bomb {
  display: flex;
  column-gap: 10px;
  justify-content: space-between;
}
.bomb__mult {
  background-color: #ebebeb;
  padding: 10px;
  border-radius: 10px;
  cursor: pointer;
}
.bomb__mult:hover {
  background-color: #dbdbdb;
}
.active {
  background-color: #d3d3d3;
}
.bet input {
  background-color: #f8f8f8;
  border: none;
  padding: 12px 0;
  font-size: 15px;
  width: 100%;
  padding-left: 20px;
  border-radius: 10px;
  user-select: none;
}
.btn__start {
  background-color: #f8f8f8;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 15px 0;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-size: 20px;
}
.btn__start:hover {
  background-color: #e6e6e6;
}
.error__two {
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
  transition: 0.5s;
  opacity: 0;
}
.error__two span {
  width: 6px;
  height: 100%;
  position: absolute;
  left: 0;
  background-color: rgb(226, 76, 76);
}
.show {
  opacity: 1;
}
.active__bomb {
  background-color: rgb(235, 122, 122);
}
.active__bomb:hover {
  background-color: rgb(235, 122, 122);
}
.no__active__bomb {
  background-color: rgb(122, 175, 235);
}
.no__active__bomb:hover {
  background-color: rgb(122, 175, 235);
}
</style>
