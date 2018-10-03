<template lang="html">
  <div>
    <json-string-modal/>
    <timer
      v-if="$route.fullPath.includes('bid')"
      :date-number="auctionDateEnd"
      date-type="reveal" />
    <timer
      v-if="$route.fullPath.includes('bid') || $route.fullPath.includes('reveal')"
      :date-number="auctionDateEnd"
      date-type="auction" />
    <div class="name-available-container">
      <div
        v-if="$route.fullPath.includes('auction')"
        class="content-header">
        <div>
          <h3> {{ domainName }}.eth </h3>
          <p>Cheers! This Domain is available.</p>
        </div>
      </div>
      <div
        v-if="$route.fullPath.includes('bid')"
        class="auction-started">
        <div>
          <h3> An auction has been started for {{ domainName }}.eth </h3>
        </div>
      </div>
      <div
        v-if="$route.fullPath.includes('reveal')"
        class="auction-started">
        <h3>
          Reveal your bid for {{ domainName }}.eth now. <br>
          {{ highestBidder }} ETH (Current highest bid)
        </h3>
      </div>
      <form class="form">
        <div class="input-container">
          <label for="localBidAmount">Actual Bid Amount</label>
          <input
            v-model="localBidAmount"
            type="number"
            name="localBidAmount">
        </div>
        <div class="input-container">
          <label for="localBidMask">Bid Mask</label>
          <input
            v-model="localBidMask"
            :class="[localBidAmount >= localBidMask ? 'errored': '']"
            type="number"
            name="localBidMask">
          <p
            v-show="localBidAmount >= localBidMask"
            class="erroredMsg">We recommend having your Bid mask higher than your Bid amount.</p>
        </div>
        <div class="input-container">
          <label
            for="localSecretPhrase"
            class="secret-phrase-label">
            <span> Secret Phrase </span>
            <span
              class="random"
              @click.prevent="generateKeyPhrase"> <i class="fa fa-lg fa-refresh"/> Random  </span>
          </label>
          <input
            v-model="localSecretPhrase"
            type="text"
            name="localSecretPhrase">
        </div>
        <div class="form-buttons">
          <button
            class="json-string"
            @click.prevent="">
            JSON string
          </button>
          <button
            type="submit"
            name="submit"
            class="submit"
            @click.prevent="$route.fullPath.includes('auction') ? startAuctionAndBid(): $route.fullPath.includes('bid') ? sendBid() : revealBid()">
            {{ $route.fullPath.includes('auction') ? 'Start Auction' : $route.fullPath.includes('bid') ? 'Bid' : 'Reveal Bid' }}
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import Timer from '../../components/Timer';
import JsonStringModal from '../../components/JsonStringModal';
export default {
  components: {
    timer: Timer,
    'json-string-modal': JsonStringModal
  },
  props: {
    domainName: {
      type: String,
      default: ''
    },
    bidAmount: {
      type: Number,
      default: 0.01
    },
    bidMask: {
      type: Number,
      default: 0.02
    },
    generateKeyPhrase: {
      type: Function,
      default: function() {}
    },
    secretPhrase: {
      type: String,
      default: 'random strings generated'
    },
    startAuctionAndBid: {
      type: Function,
      default: function() {}
    },
    sendBid: {
      type: Function,
      default: function() {}
    },
    revealBid: {
      type: Function,
      default: function() {}
    },
    auctionDateEnd: {
      type: Number,
      default: 0
    },
    highestBidder: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      localSecretPhrase: this.secretPhrase,
      localBidAmount: this.bidAmount,
      localBidMask: this.bidMask
    };
  },
  watch: {
    localSecretPhrase(newVal) {
      this.$emit('updateSecretPhrase', newVal);
    },
    localBidAmount(newVal) {
      this.$emit('updateBidAmount', +newVal);
    },
    localBidMask(newVal) {
      this.$emit('updateBidMask', +newVal);
    },
    secretPhrase(newVal) {
      this.localSecretPhrase = newVal;
    }
  },
  methods: {
    updateJson(val) {
      const json = JSON.parse(val);
      this.localSecretPhrase = json['secretPhrase'];
      this.localBidAmount = json['bidAmount'];
      this.localBidMask = json['bidMask'];
    }
  }
};
</script>

<style lang="scss" scoped>
@import 'EnsBidContainer.scss';
</style>
