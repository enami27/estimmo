<template>
  <div id="app">
    <img src="./assets/logo.png"/>
    <EstimateurImmo />
  </div>
</template>

<script>
import EstimateurImmo from './components/EstimateurImmo.vue'; 

export default {
  name: 'App',
  components: {
    EstimateurImmo
  }
}
</script>

<style scoped>

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #330E0E;
  margin-top: 60px;
}

img {
  height: auto;
  width: 500px;
}
</style>
