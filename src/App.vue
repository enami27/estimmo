<template>
  <div id="app">
    <h1>Estimation Immobilier</h1>
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
  color: #2c3e50;
  margin-top: 60px;
}

h1 {
  margin-bottom: 2rem;
}
</style>
