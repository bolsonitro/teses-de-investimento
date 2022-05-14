<template>
  <div class="main">
    <h1>Teses de investimento</h1>
    <p>Este site se destina a propagar algumas teses de investimento.</p>
    <NuxtLink to="/microstrategy">Microstrategy</NuxtLink>
    <NuxtLink to="/bolsonaro2022">Bolsonaro2022</NuxtLink>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'IndexPage',
  head: {
    title: 'Teses de investimento',
    meta: [{ charset: 'utf-8' }],
  },
})
</script>

<style>

</style>