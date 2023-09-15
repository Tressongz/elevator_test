<template>
  <div class="wrapper">
    <div class="house">
      <div
          class="shaft"
          v-for="shaft in elevators"
          :key="shaft.id"
      >
        <Elevator
            v-bind:id="`elevator_${shaft.id}`"
            :floor='shaft.floor'
            :direction="shaft.direction"
            :doorState="shaft.doorState"
        />
        <Floor v-for="floor in floors" :key="floor"/>
      </div>
    </div>
    <div class="panel">
      <button
          v-bind:class="{'wait': queueCalls.find(fl => fl === floor), 'inProcess': floorsWorking.find(fl => fl === floor)}"
          @click="elevatorCall(floor)"
          class="elevatorButton"
          v-for="floor in floors"
          :key="floor"
      >
        {{ floor }}
      </button>
    </div>
    <div class="queue">
      Этажи в работе: {{ queueCalls }}
    </div>
    <div class="floorCount" style="margin-right: 15px; margin-left: auto">
      Количество этажей:
      <button @click='floors--' class="optionsButton">—</button>
      {{ floors }}
      <button @click='floors++' class="optionsButton">+</button>
    </div>
    <div class="elevatorCount">
      Количество лифтов:
      <button @click='elevators.pop()' class="optionsButton">—</button>
       {{ elevators.length }}
      <button @click='addElevator' class="optionsButton">+</button>
    </div>
  </div>
</template>

<script setup>
import Elevator from "./components/Elevator.vue";
import Floor from "./components/Floor.vue";
import {onMounted, ref, watch} from "vue";

const elevators = ref([
    {id: 1, floor: 1, isFree: true, direction: 'idle', doorState: 'closed'}
])
const queueCalls = ref ([])
const floorHeight = 111
const floorsWorking = ref([])
const floors = ref(Number(localStorage.floors) ? Number(localStorage.floors) : 5)

init()

function choiceElevator(floor) {
  return elevators.value
      .filter(el => el.isFree)
      .map(el => ({id: el.id, floor: Math.abs(el.floor - floor)}))
      .sort((a,b) => a.floor - b.floor)[0].id
}

function elevatorCall(floor) {
  if (queueCalls.value.find(el => el === floor) === undefined
      && floorsWorking.value.find(el => el === floor) === undefined
      && elevators.value.find(el => el.floor === floor) === undefined) {
    queueCalls.value.push(floor)
  }
}

function controlElevators() {
  if (elevators.value.find(el => el.isFree === true) && queueCalls.value.length > 0) {
    let floor = queueCalls.value[0]
    let elevator = choiceElevator(floor)
    let time = Math.abs(floor - elevators.value[elevator - 1].floor) * 1000
    floorsWorking.value.push(floor)
    elevators.value[elevator - 1].direction = directionMove(elevator - 1, floor)
    moveElevator(elevator, floor, time)
    callDone()
    setTimeout(() => {
      elevators.value[elevator - 1].direction = 'idle'
      floorsWorking.value.shift()
      elevators.value[elevator - 1].doorState = 'open'
      setTimeout(() => {
        elevators.value[elevator - 1].doorState = 'closed'
      }, 1500)
      setTimeout(() => {
        elevators.value.find(el => el.id === elevator).isFree = true
      }, 3000)
    }, time)
  } else {
    setTimeout(() => {
      controlElevators()
    }, 1000)
  }
}

watch(queueCalls.value, () => {
  controlElevators()
})

onMounted(() => {
  refreshElevators()
  let times = queueCalls.value.length
  if (times > 0) {
    for (let i = 0; i < times; i++) {
      controlElevators()
    }
  }
})

function directionMove(elevator, targetFloor) {
  if (elevators.value[elevator].floor - targetFloor > 0) {
    return 'down'
  }
  return 'up'
}

function callDone() {
  queueCalls.value.shift()
}

function moveElevator(idElevator, targetFloor, time) {
  let height = -floorHeight * targetFloor + floorHeight + 'px'
  elevators.value.find(el => idElevator === el.id).isFree = false
  let object = document.getElementById(`elevator_${idElevator}`)
  object.style.transition = `transform ${time}ms`
  object.style.transform = `translate(0, ${height})`
  elevators.value.find(el => el.id === idElevator).floor = targetFloor
}

function refreshElevators() {
  elevators.value.forEach(item => {
    let height = item.floor * -floorHeight + floorHeight + 'px'
    let object = document.getElementById(`elevator_${item.id}`)
    item.direction = 'idle'
    item.isFree = true
    item.doorState = 'closed'
    object.style.transition = `transform 0ms`
    object.style.transform = `translate(0, ${height})`
  })
}

function addElevator() {
  let length = elevators.value.length
  elevators.value.push({id: length + 1, floor: 1, isFree: true, direction: 'idle', doorState: 'closed'})
}

function init() {
  if (localStorage.queueCalls && localStorage.queueCalls.length > 0) {
    queueCalls.value = JSON.parse(localStorage.queueCalls)
  }
  if (localStorage.elevators && localStorage.elevators.length > 0) {
    elevators.value = JSON.parse(localStorage.elevators)
  }
}

watch(elevators.value, (newElevators) => {
  localStorage.elevators = JSON.stringify(newElevators)
})
watch(queueCalls.value, (newQueueCalls) => {
  localStorage.queueCalls = JSON.stringify(newQueueCalls)
})
watch(floors, (newFloors) => {
  localStorage.floors = newFloors
})
</script>


<style lang="sass" scoped>
.wrapper
  display: flex
  flex-direction: row
.house
  display: flex
  flex-direction: row
.shaft
  position: relative
  width: 95px
  border-left: 1px solid cornflowerblue
  border-right: 1px solid cornflowerblue
  margin-right: 10px
.panel
  display: flex
  flex-direction: column-reverse
  margin-left: 10px
.elevatorButton
  padding: 10px
  border-radius: 15px
  transition: .8s
  cursor: pointer
  margin: 35px 0 35px 0
.elevatorButton:hover
  background-color: cornflowerblue
.wait
  background-color: #6d6dd0
.inProcess
  background-color: #088f08
.optionsButton
  height: 30px
  width: 30px
  padding: 5px
  cursor: pointer
</style>
