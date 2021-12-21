<template>
  <div id="app">
    <data-table />
  </div>
</template>

<script>
import table from './components/table.vue'

export default {
  name: 'App',
  components: {
    "data-table": table
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
    margin-top: 60px;
  }
</style>
