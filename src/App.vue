<template>
  <div id="app">
    <div>
      <button v-if="!account" @click="show = true">Open modal connect</button>
      <div v-else>
        Account: {{ account }} ChainId: {{ chainId }} Signature: {{ signature }}
        <button @click="disconnect">Disconnect</button>
      </div>
    </div>
    <ConnectComponent @error="onError" @response="onResponse" v-model="show" />
  </div>
</template>

<script>
import ConnectComponent from "./components/ConnectComponent.vue";
import Web3 from "web3";

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
      signature: null,
    };
  },

  methods: {
    onError(err) {
      console.debug({ err: err.message });
      console.error(err);
    },
    async onResponse({ provider, account, chainId }) {
      this.provider = provider;
      this.account = account;
      this.chainId = chainId;
      const web3 = new Web3(provider);

      const signature = await web3.eth.personal.sign(
        `I am signing my message`,
        account
      );
      this.signature = signature;
      if (localStorage.getItem("WALLETCONNECT_DEEPLINK_CHOICE")) {
        localStorage.removeItem("WALLETCONNECT_DEEPLINK_CHOICE");
      }
    },
    disconnect() {
      this.provider && this.provider.close && this.provider.close();
      this.provider = null;
      this.account = null;
      this.chainId = null;
      this.signature = null;
      // This localStorage key is set by @walletconnect/web3-provider
      // https://github.com/pancakeswap/pancake-frontend/blob/bdcb676700584f07b021679c6253dfed1db4d2ec/src/hooks/useAuth.ts#L65
      if (localStorage.getItem("walletconnect")) {
        localStorage.removeItem("walletconnect");
      }
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
