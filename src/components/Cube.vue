<template>
  <div>
    <div id="cube-container">
      <div id="cube" :style="styleStr" :class="{transition: isAnimating}">
        <figure class="front" :class="{color: isColor}"></figure>
        <figure class="back" :class="{color: isColor}"></figure>
        <figure class="right" :class="{color: isColor}"></figure>
        <figure class="left" :class="{color: isColor}"></figure>
        <figure class="top" :class="{color: isColor}"></figure>
        <figure class="bottom" :class="{color: isColor}"></figure>

        <Cell v-for="cell in field" :data="cell" :transform="transform" :active="active"
              :class="{transition: isAnimating, background: !isForeground(cell.id)}"></Cell>
      </div>
    </div>

    <div id="keyboard" v-if="showKeyboard">
      <button v-for="i in [1,2,3,4,5,6,7,8,9,'CLR','X']"
              :class="{cancel: !(i < 10)}" @click="btnClick(i)">
        {{ i }}
      </button>
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
      active: -1,
      showKeyboard: false,
      field: [],
      // front back left right top bottom
      activeOnSide: [0b011010,0b010010,0b010110 , 0b011000,0b010000,0b010100 , 0b011001,0b010001,0b010101,
                     0b001010,0b000010,0b000110 , 0b001000,0b000000,0b000100 , 0b001001,0b000001,0b000101,
                     0b101010,0b100010,0b100110 , 0b101000,0b100000,0b100100 , 0b101001,0b100001,0b100101]
    }
  },
  props: {
    text: String,
    sudoku: Array,
    isColor: Boolean,
    isHighlighting: Boolean
  },
  methods: {
    restart() {
      this.field = [];
      // Generate field object arrray
      for(var i = 0; i < this.sudoku.length; i++) {
        this.field.push({id: i, val: this.sudoku[i], fixed: (this.sudoku[i]!=0)});
      }
    },
    isForeground(id) {
      if(!this.isHighlighting) return true;

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
        if(e.key == "ArrowLeft") this.active--;
        else if(e.key == "ArrowRight") this.active++;
        else if(e.key == "ArrowUp") this.active -= 3;
        else if(e.key == "ArrowDown") this.active += 3;

        if(this.active < 0) this.active += 27;
        if(this.active >= 27) this.active -= 27;
      }
      if(e.keyCode >= 49 && e.keyCode <= 57) {
        // Number
        var num = e.keyCode - 48;
        this.btnClick(num);
      }
    },
    btnClick(val) {
      this.showKeyboard = false;
      if(val == 'CLR') val = 0;
      if(this.active >= 0 && val >= 0) {
        if(!this.field[this.active].fixed)
          this.field[this.active].val = val;
          this.checkSudoku()
      }
    },
    checkSudoku() {
      // Convert to "normal" sudoku
      var a = [], b = [], c = [];
      for(var i = 0; i < 9; i++) {
        a.push(this.field[i].val);
        b.push(this.field[i+9].val);
        c.push(this.field[i+18].val);
      }
      var sudoku = a.concat(b).concat(c);
      sudoku.push(...a.slice(3,9), ...a.slice(0,3));
      sudoku.push(...b.slice(3,9), ...b.slice(0,3));
      sudoku.push(...c.slice(3,9), ...c.slice(0,3));
      sudoku.push(...a.slice(6,9), ...a.slice(0,6));
      sudoku.push(...b.slice(6,9), ...b.slice(0,6));
      sudoku.push(...c.slice(6,9), ...c.slice(0,6));

      if(this.isSolvedSudoku(sudoku)) {
        // Won
        this.isAnimating = true;
        this.transform = {x: 7200, y: 7200};
        window.setTimeout(() => {alert("Herzlichen Glückwunsch, Sie haben den SudoCube gelöst.")}, 1000);
      }
    },
    isSolvedSudoku(sudoku) {
      for(var i = 0; i < 9; i++) {
        var row = [0], col = [0], block = [0];
        for(var j = 0; j < 9; j++) {
          var r = sudoku[9*i + j];
          if(!row.includes(r)) row.push(r);

          var c = sudoku[i + 9*j];
          if(!col.includes(c)) col.push(c);
          var rowOfBlock = Math.floor(i/3)*3;
          var rowInBlock = Math.floor(j/3);
          var colOfBlock = (i%3)*3;
          var colInBlock = j%3;
          var cell = (rowOfBlock+rowInBlock)*9 + colOfBlock+colInBlock;
          var b = sudoku[cell];
          if(!block.includes(b)) block.push(b);
        }
        if(row.length != 10 || col.length != 10 || block.length != 10) {
          console.log("row=" + row.join(",") + ", col:" + col.join(",") + ", block:" + block.join(","));
          return false;
        }
      }
      return true;
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
    });
    this.$bus.$on('active', (id) => {
      if(id == -1) {
        this.active = id;
      } else {
        if(!this.field[id].fixed) {
          this.active = id;
          this.showKeyboard = true;
        }
      }
    });

    this.restart();
  },
  mounted: function() {
    window.addEventListener("mousemove", this.mouseMove, false);

    window.addEventListener("touchmove", this.touchMove, false);
    window.addEventListener("touchstart", this.touchStart, false);
    window.addEventListener("touchend", this.touchEnd, false);

    window.addEventListener("keydown", this.keyDown, false);
  },
  components: {
    Cell
  },
  watch: {
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

#cube-container {
  position: fixed;
  top: calc(50vh - 100px);
  left: calc(50vw - 100px);
  width: 200px;
  height: 200px;
  perspective: 400px;
}

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

#keyboard {
  position: fixed;
  top: calc(50vh - 110px);
  left: calc(50vw - 110px);
  width: 220px;
  height: 220px;
  z-index: 900;
  border-radius: 20px;
}
/*#keyboard::before {
  position: fixed;
  top: 0px;
  left: 0px;
  width: 100%;
  height: 100%;
  z-index: -1;
  content: " - Remember this";
  background-color: rgba(0, 0, 0, 0.6);
}*/

#keyboard button {
  width: 60px;
  height: 60px;
  margin: 6px;
  background-color: #333;
  color: #EEE;
  border: none;
  font-size: 1.5em;
  border-radius: 100%;
}

#keyboard .cancel {
  background-color: #888;
}

</style>
