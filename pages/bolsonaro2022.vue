<template>
  <div class="main">
    <h1>Tese de investimento em BOLSONARO2022</h1>
    <div>
      <label for="number">Investimento Inicial em US$</label>
      <input type="number" v-model="investimentoInicial" />
      <p>Em reais: {{ equivalenteEmBrl }}</p>
    </div>
    <p>Preço de BOLSONARO2022: {{ precoToken }}</p>
    <div class="slider">
      <vue-slider v-model="precoToken" v-bind="options" />
    </div>
    <div class="cenarios">
      <div class="cenario">
        <h2>Cenário 1: Vitória de Bolsonaro</h2>
        <p>Retorno de US$ 1 para cada token</p>
        <p>
          Retorno de: US$ {{ retorno.toFixed(2) }} ou R$
          {{ (retorno / brzPrice).toFixed(2) }}
        </p>
        <p>Porcentagem de lucro {{ porcentagemDeLucro }}%</p>
      </div>
      <div class="cenario cenario2">
        <h2>Cenário 2: Derrota de Bolsonaro</h2>
        <p>Ok, você perdeu tudo</p>
        <p>Perda de 100%</p>
      </div>
    </div>
    <p>
      Token negociado
      <a href="https://ftx.com/referrals#a=bolsonitrobitcoiner" target="_blank"
        >aqui</a
      >
    </p>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import VueSlider from 'vue-slider-component/src/vue2-slider.vue'
import { Iftx } from '~/interfaces'
export default Vue.extend({
  name: 'Bolsonaro',
  components: {
    VueSlider,
  },
  data() {
    return {
      precoToken: 0,
      investimentoInicial: 100,
      brzPrice: 0,
      options: {
        min: 0,
        max: 1,
        interval: 0.01,
        width: 300,
      },
    }
  },
  async mounted() {
    const [{ data: bolsonaro }, { data: brz }] = [
      await this.$axios.get<Iftx>('/bolsonaro'),
      await this.$axios.get<Iftx>('/brz'),
    ]
    this.precoToken = bolsonaro.price
    this.brzPrice = brz.price
  },
  computed: {
    equivalenteEmBrl(): string {
      return (this.investimentoInicial / this.brzPrice).toFixed(2)
    },
    tokensEmHold(): number {
      return this.investimentoInicial / this.precoToken
    },
    retorno(): number {
      return this.tokensEmHold * 1
    },
    porcentagemDeLucro(): string {
      return (
        ((this.retorno - this.investimentoInicial) / this.investimentoInicial) *
        100
      ).toFixed(2)
    },
  },
})
</script>

<style>
.slider {
  margin-top: 30px;
}

.cenarios {
  display: flex;
  margin-bottom: 50px;
}

.cenario {
  background: rgb(184, 181, 231);
  box-shadow: 1px 40px 40px hsl(0deg, 0%, 49%);
  border-radius: 5px;
  padding: 20px
}

.cenario2 {
  margin-left: 30px;
}
</style>