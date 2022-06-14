<template>
  <div id="app">
    <h1>Safe address:</h1>
    <p>
      {{ safeInfo.safeAddress }}
    </p>
    <p>erf3CwQdPsiA6KrGbdbjhA</p>
    <div>
      <input v-model="bityApiKey" />
      <button @click="connectBity">auth</button>
    </div>
    <button v-if="!isOrderSectionHidden" @click="createOrder">Order</button>
  </div>
</template>

<script>
import SafeAppsSDK from '@gnosis.pm/safe-apps-sdk';
// import axios from 'axios';
import { BityApiClient } from '@bity/api';
import { Order, Owner } from '@bity/api/models';

// const API_DEFAULT_LINK = 'https://exchange.api.bity.com';

export default {
  name: 'App',

  data() {
    return {
      appsSdk: {},
      bityApiKey: '',
      bity: {},
      bityClientId: '',
      safeInfo: {},
      isOrderSectionHidden: true,
    };
  },

  async mounted() {
    await this.loadSdk();
  },

  methods: {
    async loadSdk() {
      this.appsSdk = new SafeAppsSDK();
      try {
        this.safeInfo = await this.appsSdk.safe.getInfo();
      } catch (e) {
        console.log(e);
      }
    },

    async connectBity() {
      this.bity = await new BityApiClient({
        exchangeApiUrl: 'https://exchange.api.bity.com',
        bookmarksApiUrl: this.bityApiKey,
      });

      this.isOrderSectionHidden = false;
    },

    async createOrder() {
      const ibanNum = 'CH0400766000103138557';
      const order = new Order();
      const result = order
        .setInput('CHF', '50.00')
        .do((input) => input.setOwner(new Owner()).setIban(ibanNum))
        .setOutput('ETH')
        .do((output) =>
          output.setCryptoAddress('0x8ED80CCF20F1E284eb56F2Ea225636F1aAC647Ce')
        );
      const preparedOrder = await result.generateObjectForOrderCreation();
      const res = await this.bity.createOrder(preparedOrder);
      const paymentDetails = await this.bity.fetchOrderWithUrl(res);
      console.log(paymentDetails, res);
      // const link = API_DEFAULT_LINK + res;
      // console.log(link);
      // const { data } = await axios.get(link);
      // console.log(data);
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
