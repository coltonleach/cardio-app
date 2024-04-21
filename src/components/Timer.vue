<script setup>
import { ref } from 'vue'

const partLog = ref([])
const activePart = ref({})
const status = ref('inactive')
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
  status.value = 'active'
  updateGoal()
  timer.value = setInterval(() => {
    difference.value = goalDate.value - new Date()
    if (difference.value <= 0) {
      if (partLog.value.length <= activePart.value.id + 1) {
        clearInterval(timer.value)
      }
      handleNext()
    } else {
      minutes.value = Math.floor(
        (difference.value % (1000 * 60 * 60)) / (1000 * 60)
      )
      seconds.value = Math.floor((difference.value % (1000 * 60)) / 1000)
    }
  }, 1000)
}

const handleStop = () => {
  clearInterval(timer.value)
  status.value = 'inactive'
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
  status.value = 'inactive'
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
  <div class="top-btn-container">
    <button
      class="start"
      :disabled="partLog.length <= 0"
      @click="handleStart"
      ref="startBtn"
    >
      Start
    </button>
    <button class="stop" @click="handleStop" ref="stopBtn">Stop</button>
    <button @click="handleClearLog">Clear</button>
  </div>
  <p class="active" v-if="status == 'active'">
    <span>{{ getType(activePart.type) }}</span> for {{ minutes }}m{{ seconds }}s
  </p>
  <div v-if="status != 'active'" class="time-container">
    <div>
      <input
        type="number"
        name="minutes"
        id="minutes"
        min="00"
        max="60"
        value="0"
        ref="minutesInput"
      />
      <p>min</p>
    </div>
    <div>
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
      <p>sec</p>
    </div>
  </div>
  <p class="up-next" v-if="partLog[activePart.id + 1] && status != 'inactive'">
    Up next: {{ getType(partLog[activePart.id + 1].type) }} for
    {{ partLog[activePart.id + 1].minutes }}m{{
      partLog[activePart.id + 1].seconds
    }}s
  </p>
  <p v-if="partLog.length > 0" class="current-status">
    On part {{ activePart.id + 1 }} / {{ partLog.length }}
  </p>
  <div class="add-btn-container">
    <button @click="handleAdd('walk')">Add Walk</button>
    <button @click="handleAdd('jog')">Add Jog</button>
    <button @click="handleAdd('run')">Add Run</button>
  </div>
  <div class="step-btn-container">
    <button :disabled="activePart.id <= 0" @click="handlePrevious">
      Previous
    </button>
    <button :disabled="partLog.length <= activePart.id + 1" @click="handleNext">
      Next
    </button>
  </div>
  <ol class="parts">
    <li v-for="part in partLog" :class="part.active ? 'current-part' : ''">
      {{ getType(part.type) }} for {{ part.minutes }}m{{ part.seconds }}s
    </li>
  </ol>
</template>

<style lang="scss">
button {
  padding: 0.75rem 1.5rem;
  font-weight: 600;
  border-radius: 0.5rem;
  background: hsl(232, 100%, 74%);
  color: hsl(263, 100%, 24%);
  box-shadow: inset 0 3px 0 hsl(232, 100%, 85%), 0 3px 6px hsla(0, 0%, 0%, 0.15),
    0 2px 4px hsla(0, 0%, 0%, 0.12);
  border: none;
  transition: filter 150ms linear;

  &:hover {
    cursor: pointer;
  }

  &.start {
    background-color: hsla(133, 41%, 54%, 1);
    color: hsla(151, 87%, 15%, 1);
    box-shadow: inset 0 3px 0px hsl(133, 41%, 75%),
      0 2px 6px hsla(0, 0%, 0%, 0.15), 0 2px 4px hsla(0, 0%, 0%, 0.12);
  }

  &.stop {
    color: hsla(0, 80%, 30%, 1);
    background-color: hsla(0, 80%, 83%, 1);
    box-shadow: inset 0 3px 0px hsl(340, 60%, 90%),
      0 2px 6px hsla(0, 0%, 0%, 0.15), 0 2px 4px hsla(0, 0%, 0%, 0.12);
  }

  &:disabled {
    filter: saturate(0);
    cursor: not-allowed;
  }
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type='number'] {
  font-size: 5rem;
  font-weight: 600;
  width: 8rem;
  height: 8rem;
  -moz-appearance: textfield;
  text-align: center;
  border-radius: 0.5rem;
  border: 2px solid var(--white);
  color: var(--white);
  background-color: var(--black);
  transition: border-color 150ms ease-in-out;

  &:focus {
    outline: none;
    border-color: #b2b6c1;
  }
}

.top-btn-container {
  display: flex;
  justify-content: space-evenly;
  margin-top: 2rem;
}

.active {
  text-align: center;
  font-size: 2rem;
  font-weight: 500;

  span {
    font-weight: 700;
  }
}

.time-container {
  display: flex;
  justify-content: center;
  text-align: center;
  gap: 1rem;
}

.up-next {
  text-align: center;
}

.current-status {
  text-align: center;
}

.add-btn-container {
  display: flex;
  gap: 1rem;

  button {
    padding: 0.75rem 1rem;
  }
}

.step-btn-container {
  display: flex;
  gap: 1rem;

  button {
    width: 100%;
  }
}

.parts {
  li {
    margin: 1rem;
    font-style: italic;

    &.current-part {
      font-size: 1.1rem;
      font-style: normal;
      font-weight: 700;
    }
  }
}
</style>
