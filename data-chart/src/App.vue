<template>
  <div id="app">
    <ChartData />
  </div>
</template>

<script>
import ChartData from "./components/ChartData.vue";

export default {
  name: "App",
  components: {
    ChartData,

  },
};
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
