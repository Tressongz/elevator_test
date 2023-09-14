<template>
  <div class="house">
    <div class="shaft" v-for="shaft in elevators">
<!--      Добавить ключ-id, по которому будет происходить управление лифтом, так же надо научиться брать значение bottom -->
      <Elevator id="elevator"/>
      <Floor v-for="floor in floors"/>
    </div>
    <button @click="changeFloorUp">Подними</button>
    <button @click="changeFloorDown">Опусти</button>
<!--    <div class="panel"></div> Тут будут кнопки для вызова лифта-->
  </div>
</template>

<script setup>
import Elevator from "./components/Elevator.vue";
import Floor from "./components/Floor.vue";
import {ref} from "vue";
//Добавить массив объектов-лифтом (айди, высота..)
const elevators = ref(2)
const floors = ref(6)
const currentFloor = ref(1)
// Добавить плавное перемещение лифта.
function changeFloorUp() {
  setTimeout(() => {
    currentFloor.value += 1
    let height = 111 * currentFloor.value - 111 + 'px'
    document.getElementById('elevator').style.bottom = height
  }, 1000)

}

function changeFloorDown() {
  setTimeout(() => {
    currentFloor.value -= 1
    let height = 111 * currentFloor.value - 111 + 'px'
    document.getElementById('elevator').style.bottom = height
  }, 1000)

}
</script>


<style lang="sass" scoped>
.house
  display: flex
  flex-direction: row
.shaft
  position: relative
  width: 95px
  border-left: 1px solid cornflowerblue
  border-right: 1px solid cornflowerblue
  margin-right: 10px
</style>
