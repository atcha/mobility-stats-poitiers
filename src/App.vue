<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>Les données mobilité</h1>
    <MobilityGraph />
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import MobilityGraph from './components/MobilityGraph.vue'

export default {
  name: 'app',
  components: {
    HelloWorld,
    MobilityGraph
  }
}
</script>

<style>
  @import "https://api.tiles.mapbox.com/mapbox-gl-js/v0.51.0/mapbox-gl.css";
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
