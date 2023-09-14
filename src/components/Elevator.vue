<template>
  <div class="elevator move"></div>
</template>

<script setup>

</script>

<style lang="sass" scoped>
.elevator
  position: absolute
  bottom: 0
  width: 95px
  height: 110px
  background-color: limegreen
  box-sizing: content-box
  -webkit-transition: -webkit-transform 1000ms
  transform: translate(0, 0)
  transition: transform 1000ms
.elevator.move
  transform: translate(0, 0px)
</style>