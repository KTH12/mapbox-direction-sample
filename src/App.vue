<template>
  <div id="app">
    <HelloWorld msg="hi map web"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  min-height: 100vh;
  width: 100%;
}
</style>
