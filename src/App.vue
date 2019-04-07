<template>
  <div id="app">
    <game/>
  </div>
</template>

<script>
import game from './components/game.vue'

export default {
  name: 'app',
  components: {
    game:game
  }
}
</script>

<style>
html, body{
  margin:0;
  padding:0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
