<template>
  <div id="cube">
    <figure class="front">{{text}}</figure>
    <figure class="back">{{text}}</figure>
    <figure class="right">{{text}}</figure>
    <figure class="left">{{text}}</figure>
    <figure class="top">{{text}}</figure>
    <figure class="bottom">{{text}}</figure>
  </div>
</template>

<script>
export default {
  data: function() {
    return {

    }
  },
  props: {
    text: String
  }
}
</script>

<style scoped>
#cube {
width: 100%;
height: 100%;
position: absolute;
transform-style: preserve-3d;
}

#cube figure {
margin: 0;
width: 196px;
height: 196px;
display: block;
position: absolute;
border: 2px solid black;
}

#cube .front  { transform: rotateY(   0deg ) translateZ( 100px ); }
#cube .back   { transform: rotateX( 180deg ) translateZ( 100px ); }
#cube .right  { transform: rotateY(  90deg ) translateZ( 100px ); }
#cube .left   { transform: rotateY( -90deg ) translateZ( 100px ); }
#cube .top    { transform: rotateX(  90deg ) translateZ( 100px ); }
#cube .bottom { transform: rotateX( -90deg ) translateZ( 100px ); }
</style>
