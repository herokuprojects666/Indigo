<template>
  <div id="app">
    <Indigo></Indigo>
  </div>
</template>

<script>
import Indigo from './components/Indigo.vue'

export default {
  name: 'App',
  components: {
    Indigo
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
