<template>
  <div id="app">
    <People/>
  </div>
</template>

<script>
import People from './components/People.vue'

export default {
  name: 'app',
  components: {
    People
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
