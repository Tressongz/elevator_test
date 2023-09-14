<template>
  <div class="elevator">
    <span
        class="display"
        v-bind:class="{'up': direction === 'up', 'down': direction === 'down'}"
    >
      {{ floor }}
    </span>
    <div class="doors">
      <div class="left" v-bind:class="{'openLeft': doorState === 'open', 'closeLeft': doorState === 'closed'}"></div>
      <div class="right" v-bind:class="{'openRight': doorState === 'open', 'closeRight': doorState === 'closed'}"></div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  floor: Number,
  direction: String,
  doorState: String
})
</script>

<style lang="sass" scoped>
.elevator
  position: absolute
  bottom: 0
  width: 95px
  height: 110px
  background-color: #5dea5d
  box-sizing: content-box
  -webkit-transition: -webkit-transform 1000ms
  transform: translate(0, 0)
  transition: transform 1000ms
  overflow: hidden
.display
  display: flex
  justify-content: center
  background-color: #0de86a
  border-bottom: 1px solid grey
.up::before
  content: '↑ '
.down::before
  content: '↓ '
.doors
  display: flex
  flex-direction: row
  height: 100%
.left
  width: 50%
  height: 100%
  background-color: #66ec74
  border-right: 1px solid grey
  -webkit-transition: -webkit-transform 1500ms
  transform: translate(0, 0)
  transition: transform 1500ms
.right
  width: 50%
  height: 100%
  background-color: #66ec74
  border-left: 1px solid grey
  -webkit-transition: -webkit-transform 1500ms
  transform: translate(0, 0)
  transition: transform 1500ms
.openLeft
  transform: translate(-100%, 0)
.openRight
  transform: translate(100%, 0)
.closeLeft
  transform: translate(0, 0)
.closeRight
  transform: translate(0, 0)
</style>