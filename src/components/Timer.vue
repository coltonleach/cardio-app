<script setup>
import { ref } from 'vue'

const partLog = ref([])
const activePart = ref({})
const goalDate = ref(null)
const timer = ref(null)
const startBtn = ref(null)
const stopBtn = ref(null)
const minutes = ref(0)
const seconds = ref(0)
const minutesInput = ref(null)
const secondsInput = ref(null)
const difference = ref(null)

const getType = (type) => {
  if (type === 'walk') return 'Walking'
  else if (type === 'jog') return 'Jogging'
  else if (type === 'run') return 'Running'
}

const updateGoal = () => {
  goalDate.value = new Date(
    new Date().getTime() + minutes.value * 60 * 1000 + seconds.value * 1000
  )
}

const handleStart = () => {
  updateGoal()
  timer.value = setInterval(() => {
    difference.value = goalDate.value - new Date()
    if (difference.value <= 0) {
      if (partLog.value.length <= activePart.value.id + 1) {
        clearInterval(timer.value)
      }
      handleNext()
    }
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
  minutes.value = 0
  seconds.value = 0
}

const handlePrevious = () => {
  if (activePart.value.id + 1 > 0) {
    partLog.value[activePart.value.id].active = false
    partLog.value[activePart.value.id - 1].active = true
    activePart.value = partLog.value[activePart.value.id - 1]
    minutes.value = activePart.value.minutes
    seconds.value = activePart.value.seconds
  }
}

const handleNext = () => {
  if (partLog.value.length > activePart.value.id + 1) {
    partLog.value[activePart.value.id].active = false
    partLog.value[activePart.value.id + 1].active = true
    activePart.value = partLog.value[activePart.value.id + 1]
    minutes.value = activePart.value.minutes
    seconds.value = activePart.value.seconds
  }
  updateGoal()
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
    pattern="[0-9]{2}"
    ref="minutesInput"
  />
  <input
    type="number"
    name="seconds"
    id="seconds"
    min="00"
    max="59"
    value="0"
    step="5"
    ref="secondsInput"
  />
  <code>current time: {{ `${minutes}:${seconds}` }}<br /></code>
  <button @click="handleAdd('walk')">add walk</button>
  <button @click="handleAdd('jog')">add jog</button>
  <button @click="handleAdd('run')">add fast</button>
  <button @click="handleClearLog">clear log</button>
  <button :disabled="activePart.id <= 0" @click="handlePrevious">prev</button>
  <button :disabled="partLog.length <= activePart.id + 1" @click="handleNext">
    next
  </button>
  <br />
  <ol class="parts">
    <li v-for="part in partLog">
      {{ getType(part.type) }} for {{ part.minutes }}m{{ part.seconds }}s -
      {{ part.active }}
    </li>
  </ol>
</template>
<style></style>
