<template>
  <div>
    <div class="control" @click="">

    </div>
    <div class="container">

    </div>
  </div>
</template>

<script>
import Cube from "./Cube.vue"

export default {
  data: function() {
    return {
      sudoku: [1,2,3,4,5,6,7,8,9 , 6,7,8,9,1,2,3,4,5 , 5,9,4,8,3,7,2,6,1],
      //sudoku: [1,0,3,0,5,6,0,8,9 , 6,7,0,0,1,2,0,0,5 , 0,9,4,0,0,0,2,0,1],
      field: []
    }
  },
  props: {
    user: String,
    isColor: Boolean
  },
  components: {
    Cube
  },
  created() {
    for(var i = 0; i < this.sudoku.length; i++) {
      this.field.push({id: i, val: this.sudoku[i]});
    }
  }
}
</script>

<style scoped>
</style>
