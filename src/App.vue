<template>
  <div id="app">
    <!--img alt="Vue logo" src="./assets/logo.png"-->
    <core/>
  </div>
</template>

<script>
import core from './components/core.vue'

export default {
  name: 'app',
  components: {
    core
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
}
</style>
