<template>
  <div class="modal-container">
    <b-modal
      ref="confirmation"
      hide-footer
      centered
      class="bootstrap-modal-wide confirmation-modal nopadding"
      title="Confirmation">
      <div class="modal-content qrcode-modal">
        <div class="tx-info">
          <div class="tx-data tx-from">
            <!-- <img src="~@/assets/images/icons/eth.svg">
            <h3>1.00000 <span>ETH</span></h3> -->
            <div class="address-info">
              <p class="address-title">From Address</p>
              <p>{{ from }}</p>
            </div>
          </div>
          <div
            v-show="to !== '' && to !== undefined"
            class="direction">
            <img src="~@/assets/images/icons/right-arrow.svg">
          </div>
          <div
            v-show="to !== '' && to !== undefined"
            class="tx-data tx-to">
            <!-- <img src="~@/assets/images/icons/btc.svg">
            <h3>0.006345 <span>BTC</span></h3> -->
            <div class="address-info">
              <p class="address-title">To Address</p>
              <p>{{ to }}</p>
            </div>
          </div>
        </div>
        <div class="detail-info">
          <div class="info">
            <h4>Detail Information</h4>
            <div class="sliding-switch-white">
              <label class="switch">
                <input
                  type="checkbox"
                  @click="modalDetailInformation = !modalDetailInformation" >
                <span class="slider round"/>
              </label>
            </div>
          </div>
          <div
            v-if="modalDetailInformation"
            class="expended-info">
            <div class="grid-block">
              <p>Network</p><p>{{ $store.state.network.type.name }} by {{ $store.state.network.service }}</p>
            </div>
            <div class="grid-block">
              <p>Gas Limit</p><p>{{ gas }} wei</p>
            </div>
            <div class="grid-block">
              <p>Gas Price</p><p>{{ gasPrice }} gwei</p>
            </div>
            <div class="grid-block">
              <p>Transaction Fee</p><p> {{ fee }} ETH</p>
            </div>
            <div class="grid-block">
              <p>Nonce</p><p>{{ nonce }}</p>
            </div>
            <div class="grid-block">
              <p>Data</p><p>{{ data }}</p>
            </div>
          </div>
        </div>

        <div class="submit-button-container">
          <div class="flex-center-align">
            <div class="button-with-helper">
              <div
                ref="ConfirmAndSendButton"
                :class="[signedTx !== ''? '': 'disabled','submit-button large-round-button-green-filled clickable']"
                @click="sendTx">
                Confirm and Send
              </div>
              <div class="tooltip-box-2">
                <b-btn id="exPopover9">
                  <img
                    class="icon"
                    src="~@/assets/images/icons/qr-code.svg">
                </b-btn>
                <b-popover
                  target="exPopover9"
                  triggers="hover focus"
                  placement="top">
                  <div class="qrcode-contents">
                    <p class="qrcode-title">Scan QR code to send/swap instantly</p>
                    <div class="qrcode-block">
                      <qrcode
                        :options="{ size: 100 }"
                        value="Hello, World!"/>
                    </div>
                    <p class="qrcode-helper">What is that?</p>
                  </div>
                </b-popover>
              </div>
            </div>
          </div>
          <p class="learn-more">Have any issues? <a href="/">Learn more</a></p>
        </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
// eslint-disable-next-line
const unit = require('ethjs-unit');

export default {
  props: {
    confirmSendTx: {
      type: Function,
      default: function() {}
    },
    fee: {
      type: Number,
      default: 0
    },
    signedTx: {
      type: String,
      default: ''
    },
    data: {
      type: String,
      default: ''
    },
    from: {
      type: String,
      default: ''
    },
    gas: {
      type: Number,
      default: 0
    },
    gasPrice: {
      type: Number,
      default: 0
    },
    nonce: {
      type: Number,
      default: 0
    },
    to: {
      type: String,
      default: ''
    },
    value: {
      type: Number,
      default: 0
    },
    isHardwareWallet: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      modalDetailInformation: false,
      transactionSigned: false
    };
  },
  computed: {
    signedTransaction() {
      if (this.signedMessage) {
        return this.signedMessage;
      } else if (this.isHardwareWallet) {
        return 'Please Approve on Hardware Wallet';
      }
      return '';
    }
  },
  methods: {
    sendTx() {
      if (this.signedTx !== '') {
        this.confirmSendTx();
      }
    }
  }
};
</script>

<style lang="scss" scoped>
@import 'ConfirmModal.scss';
</style>
