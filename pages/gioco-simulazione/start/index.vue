<template>
  <div class="container">
    <div v-if="state == 'intro'">
      <h1>
        Il tuo compito è amministrare una città per farla diventare RESILIENTE:
        hai a disposizione 80.000 coin, spendili con giudizio poi metti alla
        prova il tuo lavoro... non sai esattamente quali saranno gli impatti dei
        cambiamenti climatici sulla tua città, quanto saranno gravi o quando si
        verificheranno. A seconda delle scelte che avrai fatto e a seconda degli
        eventi climatici a cui andrai incontro, la tua città resisterà per breve
        o lungo tempo...
      </h1>
    </div>
    <div v-if="state == 'afterIntro'">
      <h1>
        La tua città si trova in collina ed è attraversata da un fiume, a
        qualche km di distanza si trovano alcune foreste che rendono migliore la
        qualità dell’aria che vi circonda e la città possiede un tessuto di
        piccole e medie imprese. Purtroppo negli ultimi anni, a causa di
        violenti temporali sempre più frequenti, la tua città ha subito spesso
        esondazioni del fiume con conseguenti allagamenti urbani e delle imprese
        locali; inoltre lunghi periodi di siccità seguiti dalle intense piogge
        hanno causato sempre più frane e smottamenti del territorio bloccando
        anche vie di comunicazione importanti; in estate i tuoi concittadini
        soffrono per le ondate di calore estive e per le sempre più frequenti
        emergenze idriche.
        <br />
        <nuxt-link
          to="al-lavoro"
          class="button--grey mt-8"
          rel="noopener noreferrer"
          >Al lavoro</nuxt-link
        >
      </h1>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  data() {
    return {
      state: '',
      millis: 1000,
    }
  },
  mounted(): void {
    this.state = 'intro'
    setInterval(() => {
      this.state = 'afterIntro'
    }, 5 * this.millis)
  },
})
</script>
