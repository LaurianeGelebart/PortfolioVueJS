<template>
  <div id="app">
    <h1>Bienvenue sur mon portfolio</h1>
    <ThreeScene class="ThreeScene"/>
  </div>
</template>

<script>
import ThreeScene from './components/3DInterface.vue'

export default {
  name: 'App',
  components: {
    ThreeScene
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
  width: 100vw;
}
h1{
  color: white;
}
/* .ThreeScene{
  width: 70% !important;
} */
</style>
