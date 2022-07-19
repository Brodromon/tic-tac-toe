<template>
  <div id="app">
    <Dash />
  </div>
</template>

<script>
import Dash from "./components/Dash.vue";

export default {
  name: "App",
  components: {
    Dash,
  },
};
</script>

<style lang="scss">
#app {
  width: 100vw;
  height: 100vh;
}
</style>
