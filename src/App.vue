<template>
  <div id="app">
    <h1>
      Safe address:
    </h1>
    <p>
      {{ safeInfo.safeAddress }}
    </p>
  </div>
</template>

<script>
import SafeAppsSDK from '@gnosis.pm/safe-apps-sdk';

export default {
  name: "App",

  data() {
    return {
      appsSdk: {},
      safeInfo: {},
    }
  },

  async mounted() {
    await this.loadSdk()
  },

  methods: {
    async loadSdk() {
      const appsSdk = new SafeAppsSDK();
      try {
        this.safeInfo = await appsSdk.safe.getInfo();
      } catch (e) {
        console.log(e)
      }
    },
  }
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
