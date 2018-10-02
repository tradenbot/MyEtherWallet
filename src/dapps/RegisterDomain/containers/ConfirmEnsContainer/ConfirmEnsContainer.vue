<template>
  <div class="confirm-ens-container">
    <div class="confirmation-warning">
      You CAN NOT claim your name unless you have this information during the reveal process. We suggest that you have to save those information.
    </div>
    <div class="ens-name">
      <h2>{{ raw.name }}.eth</h2>
    </div>
    <div class="detail-info">
      <div class="detail-info-item">
        <span class="detail-title">Actual Bid Amount</span>
        <span class="detail-value">{{ raw.bidAmount }} ETH</span>
      </div>
      <div class="detail-info-item">
        <span class="detail-title">Secret Phrase</span>
        <span class="detail-value">{{ raw.secretPhrase }}</span>
      </div>
      <div class="detail-info-item">
        <span class="detail-title">Reveal Date</span>
        <span class="detail-value">{{ formatDate(raw.revealDate) }}</span>
      </div>
      <div class="detail-info-item">
        <span class="detail-title">Bid Mask</span>
        <span class="detail-value">{{ raw.bidMask }} ETH</span>
      </div>
      <div class="detail-info-item">
        <span class="detail-title">Auction Ends</span>
        <span class="detail-value">{{ formatDate(raw.auctionDateEnd) }}</span>
      </div>
      <div>
        <div class="json-container">
          <span class="json-title">JSON String</span>
          <span
            class="json-copy"
            @click="copyString">{{ $t('common.copy') }}</span>
        </div>
        <textarea
          ref="json"
          v-model="jsonText"
          class="json-content" />
      </div>
    </div>
    <div class="button-container">
      <button
        type="button"
        class="cancel-button"
        name="button"
        @click="back">Back</button>
      <button
        type="button"
        class="submit-button"
        name="button"
        @click="sendTx">{{ $t('common.confirmAndSave') }}</button>
    </div>
  </div>
</template>

<script>
// eslint-disable-next-line
const unit = require('ethjs-unit');
import { Misc } from '@/helpers';
export default {
  props: {
    raw: {
      type: Object,
      default: function() {
        return {};
      }
    },
    back: {
      type: Function,
      default: function() {
        return {};
      }
    }
  },
  data() {
    return {
      jsonText: '',
      formatDate: Misc.formatDate
    };
  },
  watch: {
    raw(val) {
      this.parseRaw(val);
    }
  },
  mounted() {
    this.parseRaw(this.raw);
  },
  methods: {
    sendTx() {
      const web3 = this.$store.state.web3;
      const dispatch = this.$store.dispatch;
      const raw = {
        data: this.raw['data'],
        from: this.raw['from'],
        to: this.raw['to'],
        value: this.raw['value'],
        gasPrice: this.raw['gasPrice'],
        gas: this.raw['gas'],
        nonce: this.raw['nonce']
      };

      web3.eth
        .sendTransaction(raw)
        .once('transactionHash', hash => {
          dispatch('addNotification', [raw.from, hash, 'Transaction Hash']);
        })
        .on('receipt', res => {
          dispatch('addNotification', [raw.from, res, 'Transaction Receipt']);
        })
        .on('error', err => {
          dispatch('addNotification', [raw.from, err, 'Transaction Error']);
        });
    },
    parseRaw(raw) {
      this.jsonText = JSON.stringify({
        name: raw.name,
        nameSHA3: raw.nameSHA3,
        bidAmount: raw.bidAmount,
        bidMask: raw.bidMask,
        value: this.$store.state.web3.utils.toWei(
          raw.bidAmount.toString(),
          'ether'
        ),
        secretPhrase: raw.secretPhrase,
        secretPhraseSHA3: raw.secretPhraseSHA3
      });
    },
    copyString() {
      this.$refs['json'].select();
      document.execCommand('copy');
      window.getSelection().removeAllRanges();
    }
  }
};
</script>

<style lang="scss" scoped>
@import 'ConfirmEnsContainer.scss';
</style>
