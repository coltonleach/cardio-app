<script>
import { ref } from 'vue'
const time = ref(null)
const goalDate = ref(null)
const timer = ref(null)
const startBtn = ref(null)
const stopBtn = ref(null)
const hours = ref(0)
const minutes = ref(1)
const seconds = ref(0)
const difference = ref(null)

const handleStart = () => {
  goalDate.value = new Date(
    new Date().getTime() +
      hours.value * 60 * 60 * 1000 +
      minutes.value * 60 * 1000 +
      seconds.value * 1000
  )
  timer.value = setInterval(() => {
    difference.value = goalDate.value - new Date()
    if (difference.value <= 0) {
      clearInterval(timer.value)
    }
    hours.value = Math.floor(
      (difference.value % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)
    )
    minutes.value = Math.floor(
      (difference.value % (1000 * 60 * 60)) / (1000 * 60)
    )
    seconds.value = Math.floor((difference.value % (1000 * 60)) / 1000)
  }, 1000)
}

const handleStop = () => {
  clearInterval(timer.value)
}
</script>
<template>
  <button @click="handleStart" ref="startBtn">start</button>
  <button @click="handleStop" ref="stopBtn">stop</button>
  <code>{{ `${hours}:${minutes}:${seconds}` }}<br /></code>
  <div>
    <button @click="minutes++">Add Min</button>
    <button @click="minutes--">Sub Min</button>
    <button @click="seconds += 30">Add 30s</button>
    <button @click="seconds -= 30">Sub 30s</button>
  </div>
</template>
<style></style>
