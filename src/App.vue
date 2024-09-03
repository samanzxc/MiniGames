<script setup>
import {ref} from 'vue'
import coinFlip from './components/coinFlip.vue'
import bombGame from './components/bombGame.vue'

let activeGame = ref('coinFlip')
</script>

<template>
  <div class="main">
    <div class="game">
      <div class="coinFlip game__name" @click="activeGame = 'coinFlip'"><span class="light" :class="{active: activeGame === 'coinFlip'}"></span>coinFlip</div>
      <div class="bomb game__name" @click="activeGame = 'bomb'"><span class="light" :class="{active: activeGame === 'bomb'}"></span>BombGame</div>
    </div>
    <div class="play__block">
      <coinFlip v-show="activeGame === 'coinFlip'"/>
      <bombGame v-show="activeGame === 'bomb'" />
    </div>
  </div>
</template>

<style scoped>
.main {
  background: linear-gradient(180deg, rgb(0, 189, 194) 0%, rgb(0, 0, 0) 99.651%);
  min-height: 100dvh;
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.play__block {
  display: flex;
  flex-direction: column;
  align-items: center;
  row-gap: 50px;
}
.game {
  background-color: #fff;
  width: 250px;
  display: flex;
  justify-content: space-between;
  column-gap: 15px;
  padding: 10px;
  border-radius: 10px;
  position: absolute;
  top: 50px;
  left: 50px;
}
.game__name {
  background-color: #eeeeee;
  border-radius: 10px;
  padding: 10px;
  cursor: pointer;
  padding-left: 20px;
  position: relative;
}
.coinFlip {
}
.light {
    position: absolute;
    left: 7px;
    top: 17px;
    border-radius: 50%;
    width: 5px;
    height: 5px;
    background-color: #bdbdbd;
}
.active{
    background-color: rgb(0, 189, 194);
}
.bomb {
}
</style>
