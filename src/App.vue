<template>
  <div id="app">
    <div>
      <button v-if="!account" @click="show = true">Open modal connect</button>
      <div v-else>
        Account: {{ account }}
        <button @click="disconnect">Disconnect</button>
      </div>
    </div>
    <ConnectComponent @error="onError" @response="onResponse" v-model="show" />
  </div>
</template>

<script>
import ConnectComponent from "./components/ConnectComponent.vue";
export default {
  name: "App",
  components: {
    ConnectComponent,
  },
  computed: {
    isMobile() {
      return /Android|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(
        navigator.userAgent
      );
    },
  },
  data() {
    return {
      show: false,
      provider: null,
      account: null,
      chainId: null,
    };
  },
  mounted() {},
  methods: {
    onError(err) {
      console.debug({ err: err.message });
      console.error(err);
    },
    onResponse({ provider, account, chainId }) {
      this.provider = provider;
      this.account = account;
      this.chainId = chainId;
    },
    disconnect() {
      this.provider = null;
      this.account = null;
      this.chainId = null;
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
