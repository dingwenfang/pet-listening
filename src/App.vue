<template>
  <b-container id="app">
    <PetAudio/>
  </b-container>
</template>

<script>
import PetAudio from './components/PetAudio.vue'

export default {
  name: 'App',
  components: {
    PetAudio
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
