<script setup>
import { ref } from 'vue'

const timeLog = ref([])
const activePart = ref({})
const goalDate = ref(null)
const timer = ref(null)
const startBtn = ref(null)
const stopBtn = ref(null)
const hours = ref(0)
const minutes = ref(0)
const seconds = ref(0)
const minutesInput = ref(null)
const secondsInput = ref(null)
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
      minutes.value = 0
      seconds.value = 0
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

const handleMinuteInput = (event) => {
  minutes.value = event.target.value
}

const handleSecondInput = (event) => {
  seconds.value = event.target.value
}

const handleAdd = (type) => {
  timeLog.value.push({
    type: type,
    minutes: minutes.value,
    seconds: seconds.value,
    id: timeLog.value.length,
    active: timeLog.value.length === 0 ? true : false,
  })
  secondsInput.value.value = 0
  minutesInput.value.value = 0
  seconds.value = 0
  minutes.value = 0
}

const handleClearLog = () => {
  timeLog.value = []
}

const handlePrevious = () => {
  console.log('prev')
}

const handleNext = () => {}
</script>
<template>
  <button :disabled="timeLog.length <= 0" @click="handleStart" ref="startBtn">
    start
  </button>
  <button @click="handleStop" ref="stopBtn">stop</button>
  <input
    type="number"
    name="minutes"
    id="minutes"
    min="00"
    max="60"
    value="0"
    ref="minutesInput"
    @input="handleMinuteInput"
  />
  <input
    type="number"
    name="seconds"
    id="seconds"
    min="00"
    max="59"
    value="0"
    ref="secondsInput"
    @change="(e) => handleSecondInput(e)"
  />
  <code>current time: {{ `${minutes}:${seconds}` }}<br /></code>
  <button @click="handleAdd('walk')">add walk</button>
  <button @click="handleAdd('jog')">add jog</button>
  <button @click="handleClearLog">clear log</button>
  <button @click="handlePrevious">prev</button>
  <button @click="handleNext">next</button>
  <br />
  <code>{{ timeLog }}</code>
</template>
<style></style>
