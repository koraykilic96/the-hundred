<template>
  <div id="app">
    <TheHundred msg="Welcome to TheHundred"/>
  </div>
</template>

<script>
import TheHundred from './components/TheHundred.vue'

export default {
  name: 'App',
  components: {
    TheHundred
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
