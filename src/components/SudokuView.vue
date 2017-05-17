<template>
  <div class="container">
    <Cell :text="user"></Cell>
  </div>
</template>

<script>
import Cell from "./Cell.vue"

export default {
  data: function() {
    return {

    }
  },
  props: {
    user: String
  },
  components: {
    Cell
  }
}
</script>

<style scoped>
.container {
  position: fixed;
  top: calc(50vh - 100px);
  left: calc(50vw - 100px);
  width: 200px;
  height: 200px;
  position: relative;
  perspective: 1000px;
}
</style>
