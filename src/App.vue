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
        <Floor v-for="floor in floors"/>
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
  </div>
</template>

<script setup>
import Elevator from "./components/Elevator.vue";
import Floor from "./components/Floor.vue";
import {ref} from "vue";

const elevators = ref([
    {id: 1, floor: 1, isFree: true, direction: 'idle', doorState: 'closed'},
    {id: 2, floor: 1, isFree: true, direction: 'idle', doorState: 'closed'},
    {id: 3, floor: 1, isFree: true, direction: 'idle', doorState: 'closed'}
])
const queueCalls = ref([])
const floorsWorking = ref([])
const floors = ref(6)

function choiceElevator(floor) { //isTrue
  let floors = []
  elevators.value.forEach((el) => {
    if (el.isFree) {
      floors.push({id: el.id, floor: Math.abs(el.floor - floor)})
    }
  })
  floors.sort((a,b) => a.floor - b.floor)
  return floors[0].id
}

function elevatorCall(floor) {
  if (queueCalls.value.find(el => el === floor) === undefined
      && floorsWorking.value.find(el => el === floor) === undefined
      && elevators.value.find(el => el.floor === floor) === undefined) {
    queueCalls.value.push(floor)
    controlElevators()
  }
}

function controlElevators() {
  if (elevators.value.find(el => el.isFree === true) && queueCalls) {
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
      setTimeout(() => { // Лифт отдыхает
        elevators.value.find(el => el.id === elevator).isFree = true
      }, 3000)
    }, time)
  } else {
    setTimeout(() => {
      controlElevators()
    }, 1000)
  }
}

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
  let height = -111 * targetFloor + 111 + 'px'
  elevators.value.find(el => idElevator === el.id).isFree = false
  let object = document.getElementById(`elevator_${idElevator}`)
  object.style.transform = `translate(0, ${height})`
  object.style.transition = `transform ${time}ms`
  elevators.value.find(el => el.id === idElevator).floor = targetFloor
}
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
</style>
