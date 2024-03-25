<template>
  <div id="app">
    <MainComp/>
  </div>
</template>

<script>
import MainComp from './components/MainComp'

export default {
  name: 'App',
  components: {
    MainComp
  }
}
</script>

<style>
#app {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
}

body {
    margin: 0;
    font-family: 'Roboto Condensed', sans-serif;
    background: linear-gradient(white, #DBDEFE) no-repeat;
}
</style>