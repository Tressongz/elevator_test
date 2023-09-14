<template>
  <div class="wrapper">
    <div class="house">
      <div
          class="shaft"
          v-for="shaft in elevators"
          :key="shaft.id"
      >
        <Elevator v-bind:id="`elevator_${shaft.id}`"/>
        <Floor v-for="floor in floors"/>
      </div>
    </div>
    <div class="panel">
      <button
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
    {id: 1, floor: 1, isFree: true},
    {id: 2, floor: 1, isFree: true},
    {id: 3, floor: 1, isFree: true}
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

function elevatorCall(floor) { //Refactoring
  if (queueCalls.value.find(el => el === floor) === undefined && floorsWorking.value.find(el => el === floor) === undefined) {
    queueCalls.value.push(floor)
  }
    if (elevators.value.find(el => el.isFree === true)) {
      let elevator = choiceElevator(queueCalls.value[0])
      let time = Math.abs(floor - elevators.value[elevator - 1].floor) * 1000
      floorsWorking.value.push(floor)
      moveElevator(elevator, queueCalls.value[0], time)
      callDone()
      setTimeout(() => {
        // Лифт окончил работу
        setTimeout(() => {
          elevators.value.find(el => el.id === elevator).isFree = true
          floorsWorking.value.shift()
        }, 3000)
      }, time)
    } else {
      setTimeout(() => {
        elevatorCall(floor)
      }, 3000)
    }

}

function callDone() { // isTrue
  queueCalls.value.shift()
}

function moveElevator(idElevator, targetFloor, time) { // isTrue
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
</style>
