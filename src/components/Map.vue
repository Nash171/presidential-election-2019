<template>
  <div class="map-container">
    <img src="../assets/map/map-default.png" />
    <img
      class="overlay"
      v-for="overlay in overlay"
      :key="overlay.id"
      :src="require('../assets/map/'+overlay.id+'-'+overlay.lead+'.png')"
      :style="{ opacity: overlay.precentage+'%' }"
    />
  </div>
</template>

<script>
  export default {
    name: 'Map',
    props: {
      overlay: Array,
    },
  }
</script>

<style scoped>
  .map-container {
    width: 100%;
    position: relative;
  }
  img {
    width: 100%;
  }
  img.overlay {
    position: absolute;
    left: 0;
    top: 0;
  }
</style>
