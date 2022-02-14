<template>
  <div id="app">
    <div>
      <button v-if="!account" @click="show = true">Open modal connect</button>
      <div v-else>
        <div>Account: {{ account }}</div>
        <div>ChainId: {{ chainId }}</div>
        <div>Signature: {{ signature }}</div>
        <button @click="sign">Sign</button>
        <button @click="sendToken">Send</button>
        <button @click="disconnect">Disconnect</button>
      </div>
    </div>
    <ConnectComponent @error="onError" @response="onResponse" v-model="show" />
  </div>
</template>

<script>
import ConnectComponent from "./components/ConnectComponent.vue";
import Web3 from "web3";
import { ethers } from "ethers";
import erc20Abi from "./erc20.json";

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
    isIOS() {
      return (
        /iPhone|iPad|iPod/i.test(navigator.userAgent) ||
        // iPad on iOS 13 detection
        (navigator.userAgent.includes("Mac") && "ontouchend" in document)
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
    },
    disconnect() {
      this.provider && this.provider.close && this.provider.close();
      this.provider = null;
      this.account = null;
      this.chainId = null;
      this.signature = null;
      // This localStorage key is set by @walletconnect/web3-provider
      // https://github.com/pancakeswap/pancake-frontend/blob/bdcb676700584f07b021679c6253dfed1db4d2ec/src/hooks/useAuth.ts#L65
      if (window.localStorage.getItem("walletconnect")) {
        window.localStorage.removeItem("walletconnect");
      }
      if (window.localStorage.getItem("WALLETCONNECT_DEEPLINK_CHOICE")) {
        window.localStorage.removeItem("WALLETCONNECT_DEEPLINK_CHOICE");
      }
    },
    async sign() {
      const web3 = new Web3(this.provider);
      this.signature = await web3.eth.personal.sign(
        `I am signing my message`,
        this.account
      );
    },
    async sendToken() {
      const library = new ethers.providers.Web3Provider(this.provider);
      const contract = new ethers.Contract(
        "0x374bde29e22c94aa5ba7868436ba684e9e191099",
        erc20Abi,
        library.getSigner()
      );
      const params = [
        "0x113F6D74966b89FB2326e6bf4efefb97d52E5252",
        "1000000000000000000",
      ];
      const rs = await contract["transfer"](...params, {
        from: this.account,
      });
      // .then((res) => res)
      // .catch((err) => {
      //   // we only care if the error is something _other_ than the user rejected the tx
      //   console.error(err);
      //   throw err?.data || err;
      // });
      return rs;
      // console.log("send transaction result:>>", rs);
      // return await rs.wait();
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
