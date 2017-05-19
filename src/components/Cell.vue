<template>
  <div class="number" :class="{shadow: isShadow}" @click="click">
    {{ getVal(val) }}
  </div>
</template>

<script>
import Cell from  "./Cell.vue"

export default {
  data: function() {
    return {
      isShadow: false
    }
  },
  props: {
    val: Number,
    transform: Object
  },
  computed: {
    styleStr() {
      return "";
      var x = this.transform.y;
      var y = this.transform.x;
      return 'transform: rotateX(' + x +
              'deg) rotateY(' + y + 'deg)'
    }
  },
  methods: {
    click() {
      this.isShadow = !this.isShadow;
    },
    getVal(val) {
      if(val == 0) return "·";
      return val;
    }
  }
}
</script>

<style scoped>
.number {
  position: absolute;
  width: 40px;
  height: 40px;
  cursor: pointer;
  font-size: 38px;
  border: 2px solid rgba(0, 0, 0, 0);
}
.number:hover {
  border-radius: 100%;
  border: 2px solid #AAA;
}
.shadow {
  border-radius: 100%;
  background-color: rgba(0,128,255, 0.75);
}
</style>
