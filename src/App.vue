<template>
  <div id="app">
    <header>
      <span>SudoCube</span>
    </header>
    <span style="width: 70vw; display:block; margin:auto;">Use touch or press control or middle mouse key to rotate the cube.</span>
    <img @click="enableFullscreen" class="fixed" src="https://image.flaticon.com/icons/svg/0/871.svg">
    <img @click="isColor = !isColor" class="fixed left" src="https://image.flaticon.com/icons/svg/67/67745.svg">
    <img @click="isHighlighting = !isHighlighting" class="fixed left lower" src="https://image.flaticon.com/icons/svg/320/320161.svg">
    <!--<img @click="isMenu = true" class="menu fixed left" src="https://image.flaticon.com/icons/svg/126/126472.svg">
    <div class="menu-dial" v-if="isMenu">
      <h1>SudoCube - Einstellungen</h1>
      <span><img @click="isColor = !isColor" src="https://image.flaticon.com/icons/svg/67/67745.svg">Farbe</span>
    </div> TODO add settings -->

    <div class="container">
      <Cube :sudoku="sudoku" :isColor="isColor" :isHighlighting="isHighlighting"></Cube>
    </div>
    <div class="buttons">
      <button @click="rotateCube({x: 0, y: 0})">FRONT</button>
      <button @click="rotateCube({y: 0, x: 90})">LEFT</button>
      <button @click="rotateCube({y: 0, x: -90})">RIGHT</button>
      <button @click="rotateCube({y: 0, x: 180})">BACK</button>
      <button @click="rotateCube({y: 90, x: 0})">TOP</button>
      <button @click="rotateCube({y: -90, x: 0})">BOTTOM</button>
      <button @click="rotateCube({y: 18, x: 22})">3D</button>
    </div>
  </div>
</template>

<script>
import Cube from "./components/Cube.vue"

export default {
  name: 'app',
  data: function() {
    return {
      user: "",
      isColor: false,
      isHighlighting: true,
      isMenu: false,
      //sudoku: [1,2,3,4,5,6,7,8,9 , 6,7,8,9,1,2,3,4,5 , 5,9,4,8,3,7,2,6,1],
      //sudoku: [1,2,3,4,5,6,7,8,9 , 6,7,8,9,0,2,3,4,5 , 5,9,4,8,3,7,2,6,1],
      sudoku: [1,0,3,0,5,6,0,8,9 , 6,7,0,0,1,2,0,0,5 , 0,9,4,0,0,0,2,0,1],
    }
  },
  components: {
    Cube
  },
  methods: {
    enableFullscreen: function() {
      console.log("Fullscreen");
      var element = document.documentElement;
      if(element.requestFullscreen) {
        element.requestFullscreen();
      } else if(element.msRequestFullscreen) {
        element.msRequestFullscreen();
      } else if(element.mozRequestFullScreen) {
        element.mozRequestFullScreen();
      } else if (element.webkitRequestFullscreen) {
        element.webkitRequestFullscreen();
      }
    },
    rotateCube(arg) {
      this.$bus.$emit('rotate', arg);
    }
  },
  created() {

  }
}
</script>

<style>
html,
body {
  height: 100%;
  width: 100%;
  overflow: hidden;
}

#app {
  width: 100%;
  height: 100%;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  overflow: hidden;
}

* {
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
    -moz-tap-highlight-color: rgba(0, 0, 0, 0);
}

header {
  position: relative;
  margin: auto;
  z-index: 900;
  padding: 10px;
}

header span {
  background-color: #333;
  box-shadow: 0px 2px 10px #888;
  color: white;
  border-radius: 10px;
  padding: 5px;
  font-size: 2.5em;
  font-weight: bold;
}


img.fixed {
  position: fixed;
  padding: 5px;
  right: 0px;
  top: 0px;
  width: 50px;
  height: 50px;
  z-index: 1000;
  transition: transform 0.5s;
  transform: scale(0.8);
}

img.fixed:hover {
  transform: scale(1);
}



.left {
  left: 0px;
}

.lower {
  top: 50px !important;
}

.container {
  width: 100%;
}

.buttons {
  position: fixed;
  bottom: 10px;
  left: 0px;
  width: 100%;
}

.buttons button {
  padding: 5px;
  min-height: 50px;
  width: 13%;
  background-color: #333;
  color: #EEE;
  border: none;
  font-size: 1.2em;
  border-radius: 10px;
}


.menu {
  transition: transform 0.5s;
}

.menu:hover {
  transform: rotate(90deg);
}

.menu-dial {
  position: fixed;
  top: 0px;
  left: 0px;
  width: 100%;
  height: 100%;
  z-index: 1000;
  background-color: white;
  text-align: left;
}

.menu-dial span {
  text-align: left;
  padding: 10px;
  font-size: 25px;
}

.menu-dial img {
  width: 35px;
}

</style>
