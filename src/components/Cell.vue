<template>
  <div class="number" :style="position" :class="{active: active == data.id, fixed: data.fixed}" @click="click">
    {{ getVal(data.val) }}
  </div>
</template>

<script>
import Cell from  "./Cell.vue"

export default {
  data: function() {
    return {

    }
  },
  props: {
    active: Number,
    data: Object,
    transform: Object
  },
  computed: {
    styleStr() {
      return "";
      var x = this.transform.y;
      var y = this.transform.x;
      return 'transform: rotateX(' + x +
              'deg) rotateY(' + y + 'deg)'
    },
    position() {
      var x = (this.data.id % 3)*64 + 18;
      var y = Math.floor(this.data.id/3)*64 - Math.floor(this.data.id/9)*64*3 + 18;
      var z = Math.floor(this.data.id/9)*64 - 64;
      var rotX = this.transform.y;
      var rotY = -this.transform.x;

      return 'transform: translateX(' + x + 'px) ' +
              'translateY(' + y + 'px) ' +
              'translateZ(' + z + 'px) ' +
              'rotateY(' + rotY + 'deg) ' +
              'rotateX(' + rotX + 'deg)';
    }
  },
  methods: {
    click() {
      if(this.active == this.data.id)
        this.$bus.$emit('active', -1);
      else
        this.$bus.$emit('active', this.data.id);
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
.active {
  border-radius: 100%;
  background-color: rgba(0,128,255, 0.75);
}
.fixed {
  border: none !important;
  cursor: default;
  background-color: rgba(0, 0, 0, 0.15);
  border-radius: 8px !important;
}
</style>
