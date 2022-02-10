<template>
  <div id="app">
    <div>
      <button @click="show = true">Open modal connect</button>
    </div>
    <ConnectComponent @error="onError" v-model="show" />
  </div>
</template>

<script>
import ConnectComponent from "./components/ConnectComponent.vue";
export default {
  name: "App",
  components: {
    ConnectComponent,
  },
  data() {
    return {
      show: false,
    };
  },
  methods: {
    onError(err) {
      console.debug({ err: err.message });
      console.error(err);
    },
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
