<template>
  <div class="main">
    <div class="spinner" v-if="isLoading"><Spinner name="circle" /></div>
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
import Spinner from 'vue-spinkit'
import { Iftx, Istock } from '@/interfaces'
export default Vue.extend({
  name: 'Microstrategy',
  components: { Spinner },
  data() {
    return {
      microstrategyPrice: 0,
      bitcoinsInMicrostrategyBalance: 115109,
      bitcoinPrice: 0,
      microstrategyShares: 11290000,
      bitcoinValuePerShareInUsd: 0,
      isLoading: false,
    }
  },
  async mounted() {
    this.isLoading = true
    await Promise.all([
      await this.$axios
        .get<Istock>('mstr')
        .then(({ data: mstr }) => {
          this.microstrategyPrice = mstr.regularMarketPrice
        })
        .catch((e) => console.log(e)),
      await this.$axios
        .get<Iftx>('bitcoin')
        .then(({ data: bitcoin }) => {
          this.bitcoinPrice = bitcoin.price
        })
        .catch((e) => console.log(e)),
    ])
    this.isLoading = false
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
.spinner {
  display: flex;
  justify-content: center;
}
</style>
