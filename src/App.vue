<template>
  <div id="app" class="container" >
    <GameCanvas tabindex="0"/>
  </div>
</template>

<script>
import GameCanvas from "./components/GameCanvas.vue";

export default {
  name: "app",
  components: {
    GameCanvas
  },
  methods: {}
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
