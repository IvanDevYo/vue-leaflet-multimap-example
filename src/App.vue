<template>
  <div id="app">
    <Map v-if="false" />
    <four-map></four-map>
  </div>
</template>

<script>
import Map from './Map.vue'
import FourMap from './FourMap.vue'

export default {
  name: 'App',
  components: {
    Map,
    FourMap,
  }
}
</script>

<style>
</style>
