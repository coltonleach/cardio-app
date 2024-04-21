<script setup>
import { ref } from 'vue'

const partLog = ref([])
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

const handleAdd = (type) => {
  partLog.value.push({
    type: type,
    minutes: minutesInput.value.value,
    seconds: secondsInput.value.value,
    id: partLog.value.length,
    active: partLog.value.length === 0 ? true : false,
  })
  if (partLog.value.length === 1) {
    activePart.value = partLog.value[0]
  }
  secondsInput.value.value = 0
  minutesInput.value.value = 0
  minutes.value = activePart.value.minutes
  seconds.value = activePart.value.seconds
}

const handleClearLog = () => {
  partLog.value = []
  activePart.value = {}
}

const handlePrevious = () => {
  if (activePart.value.id + 1 > 0) {
    activePart.value = partLog.value[activePart.value.id - 1]
    minutes.value = activePart.value.minutes
    seconds.value = activePart.value.seconds
  }
}

const handleNext = () => {
  if (partLog.value.length > activePart.value.id + 1) {
    activePart.value = partLog.value[activePart.value.id + 1]
    minutes.value = activePart.value.minutes
    seconds.value = activePart.value.seconds
  }
}
</script>
<template>
  <button :disabled="partLog.length <= 0" @click="handleStart" ref="startBtn">
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
  />
  <input
    type="number"
    name="seconds"
    id="seconds"
    min="00"
    max="59"
    value="0"
    ref="secondsInput"
  />
  <code>current time: {{ `${minutes}:${seconds}` }}<br /></code>
  <button @click="handleAdd('walk')">add walk</button>
  <button @click="handleAdd('jog')">add jog</button>
  <button @click="handleAdd('fast')">add fast</button>
  <button @click="handleClearLog">clear log</button>
  <button :disabled="activePart.id <= 0" @click="handlePrevious">prev</button>
  <button :disabled="partLog.length <= activePart.id + 1" @click="handleNext">
    next
  </button>
  <br />
  <code>{{ partLog }}</code>
  <br />
  <code>{{ activePart }}</code>
</template>
<style></style>
