<template>
  <div>
    <h1>Tese de investimento em MicroStrategy</h1>
    <p>Preço de MSTR: US$ {{ microstrategyPrice }}</p>
    <div>
      <label for="number">Bitcoins da Microstrategy: </label>
      <input type="number" v-model.lazy="bitcoinsInMicrostrategyBalance" />
    </div>
    <p>
      Valor dos bitcoins da Microstrategy por ação:
      {{ bitcoinValuePerShareInUsd.toFixed(2) }}
    </p>
    <p>Preço do Bitcoin hoje: US$ {{ bitcoinPrice }}</p>
    <p>{{ descontoOuAgio }} da MSTR em relação aos Bitcoins no balanço</p>
    <p>{{ microstrategyDiscount1 }}%</p>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: 'Microstrategy',
  data() {
    return {
      microstrategyPrice: 0,
      bitcoinsInMicrostrategyBalance: 115109,
      bitcoinPrice: 0,
      microstrategyShares: 11290000,
      bitcoinValuePerShareInUsd: 0,
    }
  },
  async mounted() {
    const [mstr, bitcoin] = [
      await this.$axios.get('http://127.0.0.1:3001/mstr'),
      await this.$axios.get('http://127.0.0.1:3001/bitcoin'),
    ]

    this.microstrategyPrice = mstr.data.regularMarketPrice
    this.bitcoinPrice = bitcoin.data.price
  },
  methods: {
    microstrategyDiscount(): number {
      const bitcoinsPerShare =
        this.bitcoinsInMicrostrategyBalance / this.microstrategyShares
      this.bitcoinValuePerShareInUsd = bitcoinsPerShare * this.bitcoinPrice

      const discount = this.microstrategyPrice / this.bitcoinValuePerShareInUsd
      return (discount - 1) * 100
    },
  },
  computed: {
    microstrategyDiscount1(): string {
      return (this.microstrategyDiscount() * -1).toFixed(2)
    },
    descontoOuAgio(): string {
      return this.microstrategyDiscount() < 1 ? 'Desconto' : 'Agio'
    },
  },
})
</script>

<style>
</style>