<template>
  <div id="main">
    <div
      v-if="isTgClient && isTgApiUpdated"
      class="text-center"
    >

      <div v-if="code">
        <h3>QR code:</h3>
        {{ code }} <br>
      </div>
      <div v-if="!code">
        <h3>Scan a QR code!</h3>
      </div>
    </div>



    <div
      v-if="!isTgClient"
      class="text-center"
    >
      Please open the app from a Telegram client!<br>
    </div>
    <div
      v-if="isTgClient && !isTgApiUpdated"
      class="text-center"
    >
      Please update Telegram to Use the app!<br>
      Telegram API version needed 6.4 or greater.<br>
      Your Telegram API version: {{ TWA.version }}
    </div>
  </div>
</template>


<script>

export default {
  data() {
    return {
      isTgClient: false,
      isTgApiUpdated: false,
      code: null,
    };
  },
  created() {
    this.TWA.MainButton.setText("Scan QR code");
    this.TWA.onEvent('qrTextReceived', this.processQRCode);
    this.TWA.onEvent('mainButtonClicked', this.mainButtonClicked);

    this.isTgApiUpdated = this.TWA.isVersionAtLeast('6.4');
    // platform not updated if version is not 6.4 or greater
    if (this.TWA.platform != "unknown") {
      this.isTgClient = true;
    }

    if (this.isTgClient && this.isTgApiUpdated) {
      this.TWA.MainButton.show();
      this.showQRScanner();
    }
  },
  mounted() {
    this.TWA.ready();
  },
  methods: {
    mainButtonClicked() {
      this.showQRScanner();
    },
    openLink() {
      this.TWA.openLink(this.url);
    },
    processQRCode(data) {
       this.code = data.data;
       this.TWA.closeScanQrPopup();

       //this.TWA.showAlert(data.data);
    },
    // End of callbacks
    showQRScanner() {
      const par = {
          text: ""
        };
      this.TWA.showScanQrPopup(par);
    },
  }
}
</script>

<style scoped>
#main {
  background-color: var(--tg-theme-bg-color, white);
  color: var(--tg-theme-text-color, black);
  word-wrap: break-word;
}
b {
  color: var(--tg-theme-hint-color, black);
}
h3 {
  color: var(--tg-theme-text-color, black);
}
button {
  background-color: var(--tg-theme-button-color, #008CBA);
  border: 5px;
  color: var(--tg-theme-button-text-color, black);
  padding: 15px;
  margin: 5px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 15px;
}
</style>
