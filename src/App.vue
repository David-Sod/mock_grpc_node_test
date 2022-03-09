<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import { SecretNetworkClient } from "secretjs";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
  async mounted() {
    try {
      // To create a readonly secret.js client, just pass in an RPC endpoint
      const secretjs = await SecretNetworkClient.create({
        grpcWebUrl: "http://rpc.pulsar.griptapejs.com:9091/",
      });

      const {
        balance: { amount },
      } = await secretjs.query.bank.balance({
        address: "secret1ap26qrlp8mcq2pg6r47w43l0y8zkqm8a450s03",
        denom: "uscrt",
      });

      console.log(`I have ${amount} SCRT!`);

      const sSCRT = "secret18vd8fpwxzck93qlwghaj6arh4p7c5n8978vsyg";
      // Get codeHash using `secretcli q compute contract-hash secret1k0jntykt7e4g3y88ltc60czgjuqdy4c9e8fzek`
      const sScrtCodeHash =
        "9587d60b8e6b078ace12014ceeee089530b9fabcd76535d93666a6c127ad8813";

      const { token_info } = await secretjs.query.compute.queryContract({
        address: sSCRT,
        codeHash: sScrtCodeHash, // optional but way faster
        query: { token_info: {} },
      });

      console.log(`sSCRT has ${token_info.decimals} decimals!`);

      const res = await secretjs.query.tendermint.getLatestBlock();
      console.log(res.block);
    } catch (error) {
      console.error(error);
    }
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
