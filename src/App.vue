<template>
  <XOREncoder />
</template>

<script>
import XOREncoder from './components/XOREncoder.vue'

export default {
  name: 'App',
  components: {
    XOREncoder
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
