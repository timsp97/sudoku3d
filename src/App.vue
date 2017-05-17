<template>
  <div id="app">
    <SudokuView :user="user"></SudokuView>
  </div>
</template>

<script>
import SudokuView from './components/SudokuView'

export default {
  name: 'app',
  data: function() {
    return {
      user: "Hallo"
    }
  },
  components: {
    SudokuView
  },
}
</script>

<style>
#app {
  width: 100%;
  height: 100%;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
