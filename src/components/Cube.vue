<template>
  <div>
    <div id="cube" :style="styleStr" :class="{transition: isAnimating}">
      <figure class="front" :class="{color: isColor}"></figure>
      <figure class="back" :class="{color: isColor}"></figure>
      <figure class="right" :class="{color: isColor}"></figure>
      <figure class="left" :class="{color: isColor}"></figure>
      <figure class="top" :class="{color: isColor}"></figure>
      <figure class="bottom" :class="{color: isColor}"></figure>

      <Cell v-for="cell in field" :val="cell.val" :style="position(cell.id)" :transform="transform"
            :class="{transition: isAnimating, background: !isForeground(cell.id)}"></Cell>
    </div>

  </div>
</template>

<script>
import Cell from  "./Cell.vue"

export default {
  data: function() {
    return {
      transform: {x: 0, y: 0},
      last: {x: 0, y: 0},
      isMove: true,
      isTouch: false,
      isAnimating: false,
      side: 0,
      // front back left right top bottom
      activeOnSide: [0b011010,0b010010,0b010110 , 0b011000,0b010000,0b010100 , 0b011001,0b010001,0b010101,
                     0b001010,0b000010,0b000110 , 0b001000,0b000000,0b000100 , 0b001001,0b000001,0b000101,
                     0b101010,0b100010,0b100110 , 0b101000,0b100000,0b100100 , 0b101001,0b100001,0b100101]
    }
  },
  props: {
    text: String,
    field: Array,
    isColor: Boolean
  },
  methods: {
    isForeground(id) {
      var active = this.activeOnSide[id];
      var side = 1 << (5-this.side);

      if((side & active) > 0) return true;
      return false;
    },
    dec2bin(dec) {
      return (dec >>> 0).toString(2);
    },
    mouseMove: function(e) {
      //console.log(e)
      if(e.button == 1 || e.ctrlKey || this.isTouch) {
        if(this.isMove || this.isTouch) {
          this.transform.y = this.last.y + (e.clientY/window.innerHeight-0.5)*360;
          this.transform.x = this.last.x + (e.clientX/window.innerWidth-0.5)*360;
        } else {
          this.mouseDown(e);
        }
      } else {
        if(this.isMove) this.mouseUp();
      }
    },
    mouseUp() {
      console.log("mouse up");
      this.isMove = false;

    },
    mouseDown(e) {
      console.log("mouse down");
      this.isAnimating = false;
      this.last.y = this.transform.y-(e.clientY/window.innerHeight-0.5)*360;
      this.last.x = this.transform.x-(e.clientX/window.innerWidth-0.5)*360;
      this.isMove = true;
    },
    touchMove(e) {
      if(this.isTouch) {
        //e.preventDefault();
        this.transform.y = this.last.y + (e.touches[0].clientY/window.innerHeight-0.5)*360;
        this.transform.x = this.last.x + (e.touches[0].clientX/window.innerWidth-0.5)*360;
      }
    },
    touchStart(e) {
      this.isAnimating = false;
      this.last.y = this.transform.y-(e.touches[0].clientY/window.innerHeight-0.5)*360;
      this.last.x = this.transform.x-(e.touches[0].clientX/window.innerWidth-0.5)*360;
      this.isTouch = true;
    },
    touchEnd() {
      this.isTouch = false;
    },
    keyDown(e) {
      if(e.ctrlKey) {
        this.isAnimating = false;
        if(e.key == "ArrowLeft") this.transform.x -= 5;
        else if(e.key == "ArrowRight") this.transform.x += 5;
        else if(e.key == "ArrowUp") this.transform.y -= 5;
        else if(e.key == "ArrowDown") this.transform.y += 5;
      } else {
        // TODO implement cursor
        // TODO add keyboard input
      }
    },
    keyUp() {

    },
    position(id) {
      var x = (id % 3)*64 + 18;
      var y = Math.floor(id/3)*64 - Math.floor(id/9)*64*3 + 18;
      var z = Math.floor(id/9)*64 - 64;
      var rotX = this.transform.y;
      var rotY = -this.transform.x;

      return 'transform: translateX(' + x + 'px) ' +
              'translateY(' + y + 'px) ' +
              'translateZ(' + z + 'px) ' +
              'rotateY(' + rotY + 'deg) ' +
              'rotateX(' + rotX + 'deg)';
    }
  },
  computed: {
    styleStr: function() {
      var x = -this.transform.y;
      var y = this.transform.x;
      return 'transform: rotateX(' + x +
              'deg) rotateY(' + y + 'deg)'
    }
  },
  created() {
    this.$bus.$on('rotate', (transform) => {
      this.isAnimating = true;
      this.transform = transform;
      console.log("centered");
    })
  },
  mounted: function() {
    window.addEventListener("mousemove", this.mouseMove, false);

    window.addEventListener("touchmove", this.touchMove, false);
    window.addEventListener("touchstart", this.touchStart, false);
    window.addEventListener("touchend", this.touchEnd, false);

    window.addEventListener("keydown", this.keyDown, false);
    window.addEventListener("keyup", this.keyUp, false);
  },
  components: {
    Cell
  },
  watch: {
    /*<button @click="rotateCube({x: 0, y: 0})">FRONT</button>
    <button @click="rotateCube({y: 0, x: 90})">LEFT</button>
    <button @click="rotateCube({y: 0, x: -90})">RIGHT</button>
    <button @click="rotateCube({y: 0, x: 180})">BACK</button>
    <button @click="rotateCube({y: 90, x: 0})">TOP</button>
    <button @click="rotateCube({y: -90, x: 0})">BOTTOM</button>*/
    transform: {
      handler: function(val) {
        var yDeg = val.y % 360;
        if(yDeg < 0) yDeg += 360;

        if(yDeg < 135 && yDeg >= 45) {
          // top
          this.side = 4;
        } else if(yDeg < 315 && yDeg >= 225) {
          // bottom
          this.side = 5;
        } else {
          var off = 0;
          if(yDeg < 225 && yDeg >= 135) off = 2;
          var xDeg = val.x % 360;
          if(xDeg < 0) xDeg += 360;

          for(var i = 0; i < 4; i++) {
            if(xDeg < 90*i+45 && xDeg >= 90*i-45) {
              this.side = [0,2,1,3][(i+off)%4];
              break;
            }
          }
        }
      },
      deep: true
    }
  }
}
</script>

<style scoped>
#cube {
  width: 100%;
  height: 100%;
  position: absolute;
  transform-style: preserve-3d;
  user-select: none;
}

#cube figure {
  margin: 0;
  width: 196px;
  height: 196px;
  display: block;
  position: absolute;
  border: 3px solid black;
  pointer-events: none;
}

#cube .front  { transform: rotateY(   0deg ) translateZ( 100px ); }
#cube .back   { transform: rotateX( 180deg ) translateZ( 100px ); }
#cube .right  { transform: rotateY(  90deg ) translateZ( 100px ); }
#cube .left   { transform: rotateY( -90deg ) translateZ( 100px ); }
#cube .top    { transform: rotateX(  90deg ) translateZ( 100px ); }
#cube .bottom { transform: rotateX( -90deg ) translateZ( 100px ); }

#cube .color.front  { border: 2px solid #F00; background-color: rgba(255, 0, 0, 0.2)}
#cube .color.back   { border: 2px solid #FF0; background-color: rgba(255, 255, 0, 0.2)}
#cube .color.right  { border: 2px solid #0F0; background-color: rgba(0, 255, 0, 0.2)}
#cube .color.left   { border: 2px solid #0FF; background-color: rgba(0, 255, 255, 0.2)}
#cube .color.top    { border: 2px solid #00F; background-color: rgba(0, 0, 255, 0.2)}
#cube .color.bottom { border: 2px solid #F0F; background-color: rgba(255, 0, 255, 0.2)}


#cube { transform: translateZ( -100px ); }

.transition {
  transition: transform 1s;
}
.background {
  opacity: 0.3;
}

</style>
