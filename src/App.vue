<script setup>
import {ref, onMounted} from 'vue'

const timers = ref([])

/**
 * timer class.
 *
 * @constructor
 * @param {Number} seconds - начальное время в секундах
 * @param {Boolean} active  - активен ли таймер
 */
class Timer {
  constructor(seconds, active) {
    this.seconds = seconds || 0
    this._active = active || false
  }

  get time() {
    let days = 0, hours = 0, minutes = 0, seconds = 0, text = ""
    // 560
    seconds = this.seconds
    days = Math.floor(seconds / 72000)
    hours = Math.floor(seconds / 3600) - days * 24
    minutes = Math.floor(seconds / 60) - hours * 60
    seconds = seconds - days * 72000 - hours * 3600 - minutes * 60
    if (this.seconds < 60) {
      text = seconds
    }
    if (minutes < 60 && hours == 0 && days == 0) {
      text = `${minutes}:${seconds < 10 ? "0" + seconds : seconds}`
    }
    if (minutes < 60 && hours > 0 && days == 0) {
      text = `${hours}:${minutes < 10 ? "0" + minutes : minutes}:${seconds < 10 ? "0" + seconds : seconds}`
    }
    if (minutes < 60 && hours > 0 && days > 0) {
      text = `${hours < 10 ? "0" + hours : hours}:${minutes < 10 ? "0" + minutes : minutes}:${seconds < 10 ? "0" + seconds : seconds}`
    }
    return {days, hours, minutes, seconds, text}
  }

  get isOnPause() {
    return this._active
  }

  start() {
    console.log("start")
    this._active = true
  }

  pause() {
    console.log("pause")
    this._active = false
  }

  reset() {
    console.log("reset")
    this.seconds = 0
    this._active = false
  }
}

const createTimer = () => {
  timers.value.push(new Timer())
}
const deleteTimer = (t) => {
  if (timers.value.findIndex(item => item == t))
    clearInterval(timers.value.find(item => item == t).interval)
  timers.value.splice(timers.value.findIndex(item => item == t), 1)
}
onMounted(() => {
  setInterval(() => {
    for (let i = 0; i < timers.value.length; i++) {
      if (timers.value[i].isOnPause) {
        timers.value[i].seconds++
      }
    }
  }, 1)
})
</script>

<template>
  <div class="container">
    <div v-for="(timer, index) in timers" :key="index" :class="{'card-active': timer.isOnPause}" class="card">
      <div class="card__header">
        {{ timer.time.text }}
        <button @click="deleteTimer(timer)"></button>
      </div>
      <div class="card__footer">
        <button v-if="timer.isOnPause" class="pause" @click="timer.pause()"></button>
        <button v-else class="start" @click="timer.start()"></button>
        <button class="reset" @click="timer.reset()"></button>
      </div>
    </div>
    <button class="card card-add" @click="createTimer"></button>
  </div>
</template>
<style>
body {
  background: #353638;
}

.card {
  background: #696969;
  width: 225px;
  height: 120px;
  position: relative;
}

.card__header {
  display: grid;
  place-items: center;
  border-bottom: 1px solid #9E9E9E;
  height: 50%;
  font-family: 'Gotham Pro';
  font-style: normal;
  font-weight: 400;
  font-size: 22px;
  line-height: 21px;
  text-align: center;
  color: #9E9E9E;
  user-select: none;
}

.card-active .card__header {
  color: #FFFFFF;
  border-bottom: 1px solid #FFFFFF;
}

.card:hover .card__header button {
  opacity: 1;
}

.card__header button {
  position: absolute;
  top: 0;
  right: 0;
  background: #696969;
  opacity: 0;
  transition: 0.2s;
  width: 30px;
  aspect-ratio: 1/1;
  border-radius: 15px;
  transform: translate(50%, -50%);
  cursor: pointer;
}

.card__header button:before,
.card__header button:after {
  box-sizing: border-box;
  content: "";
  display: block;
  width: 3px;
  height: 20px;
  background: #9E9E9E;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%) rotate(45deg);
}

.card-active .card__header button:before
.card-active .card__header button:after {
  background: #FFFFFF;
}

.card__header button:after {
  transform: translate(-50%, -50%) rotate(-45deg);
}

.card__footer {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 50px;
  height: 50%;
}

.card__footer .reset {
  background: #9E9E9E;
  cursor: pointer;
  width: 20px;
  height: 20px;
}

.card-active .card__footer .reset {
  background: #FFFFFF;
}

.card__footer .start {
  border-top: 10px solid transparent;
  border-right: 20px solid #9E9E9E;
  border-bottom: 10px solid transparent;
  background: #696969;
  transform: rotate(-180deg);
  cursor: pointer;
}

.card-active .card__footer .start {
  border-right: 20px solid #FFFFFF;
}

.card__footer .pause {
  display: flex;
  gap: 4px;
  background: none;
  cursor: pointer;
  padding: 0 5px;
}

.card__footer .pause:before,
.card__footer .pause:after {
  height: 20px;
  width: 3px;
  background: #9E9E9E;
  box-sizing: border-box;
  content: "";
  display: block;
}

.card-active .card__footer .pause:before,
.card-active .card__footer .pause:after {
  background: #FFFFFF;
}

.card-add {
  cursor: pointer;
}

.card-add:before,
.card-add:after {
  box-sizing: border-box;
  content: "";
  width: 3px;
  height: 20px;
  background: #9E9E9E;
  display: block;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.card-add:after {
  transform: translate(-50%, -50%) rotate(-90deg);
}

.container {
  display: grid;
  gap: 50px;
  padding: 72px 50px;
  place-items: center;
  justify-content: center;
}
@media screen and (min-width: 320px){
  .container{
    grid-template-columns: 225px;
  }
}
@media screen and (min-width: 768px){
  .container{
    grid-template-columns: 225px 225px;
  }
}
@media screen and (min-width: 1024px){
  .container{
    grid-template-columns: 225px 225px 225px;
  }
}
</style>