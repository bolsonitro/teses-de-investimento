<template>
  <div class="main">
    <div class="spinner" v-if="isLoading"><Spinner name="circle" /></div>
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
    <div>
      <h2>Minha posição</h2>
      <p>Custo: US$ {{ valorInvestido }} ou R$ {{ valorInvestidoEmBrl }}</p>
      <p>Perda realizada atual: US$ {{ perdaRealizada }}</p>
      <p>
        Collateral usado: US$ {{ collateralUsado }} ou R$
        {{ collateralUsadoEmBrl }}
      </p>
      <p>
        Ganho potencial: US$ {{ ganhoPotencial }} ou R$
        {{ ganhoPotencialemBrl }}
      </p>
    </div>
    Token negociado
    <a href="https://ftx.com/referrals#a=bolsonitrobitcoiner" target="_blank"
      >aqui</a
    >
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import VueSlider from 'vue-slider-component/src/vue2-slider.vue'
import Spinner from 'vue-spinkit'
import { Iftx, IPosition } from '~/interfaces'
export default Vue.extend({
  name: 'Bolsonaro',
  components: {
    VueSlider,
    Spinner,
  },
  data() {
    return {
      precoToken: 0,
      investimentoInicial: 100,
      brzPrice: 0,
      isLoading: false,
      options: {
        min: 0,
        max: 1,
        interval: 0.01,
        width: 300,
      },
      position: {
        valorInvestido: 0,
        size: 0,
        openPrice: 0,
        realizedPnl: 0,
        collateral: 0,
      },
    }
  },
  async mounted() {
    this.isLoading = true
    await Promise.all([
      this.$axios
        .get<Iftx>('/bolsonaro')
        .then(({ data: bolsonaro }) => {
          this.precoToken = bolsonaro.price
        })
        .catch((e) => console.log(e)),
      this.$axios
        .get<Iftx>('/brz')
        .then(({ data: brz }) => {
          this.brzPrice = brz.price
        })
        .catch((e) => console.log(e)),
      this.$axios
        .get<IPosition>('/position')
        .then(({ data: position }) => {
          this.position.size = position.size
          this.position.openPrice = position.recentAverageOpenPrice
          this.position.valorInvestido =
            this.position.size * this.position.openPrice
          this.position.realizedPnl = position.realizedPnl
          this.position.collateral = position.collateralUsed
        })
        .catch((e) => console.log(e)),
    ])
    this.isLoading = false
  },
  methods: {
    converterParaBrl(valor: number): number {
      return valor / this.brzPrice
    },
  },
  computed: {
    equivalenteEmBrl(): string {
      return this.converterParaBrl(this.investimentoInicial).toFixed(2)
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
    valorInvestido(): string {
      return this.position.valorInvestido.toFixed(2)
    },
    valorInvestidoEmBrl(): string {
      return this.converterParaBrl(this.position.valorInvestido).toFixed(2)
    },
    perdaRealizada(): string {
      return this.position.realizedPnl.toFixed(2)
    },
    ganhoPotencial(): string {
      return this.position.size.toFixed(2)
    },
    ganhoPotencialemBrl(): string {
      return this.converterParaBrl(this.position.size).toFixed(2)
    },
    collateralUsado(): string {
      return this.position.collateral.toFixed(2)
    },
    collateralUsadoEmBrl(): string {
      return this.converterParaBrl(this.position.collateral).toFixed(2)
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
  padding: 20px;
}

.cenario2 {
  margin-left: 30px;
}

.spinner {
  display: flex;
  justify-content: center;
}
</style>