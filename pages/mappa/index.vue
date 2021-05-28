<template>
  <div class="relative">
    <nuxt-link
      to="/"
      class="absolute left-0 top-0 rounded-xl border-red-700 hover:border-white hover:text-white hover:bg-red-700 p-3.5 m-3.5 border-2 text-red-700"
    >
      Torna a Home</nuxt-link
    >
    <img
      :src="
        choose == -1
          ? require('~/assets/MAP.jpeg')
          : require(`~/assets/MAPPE/MAP${choose}.jpg`)
      "
      width="1920px"
      height="1080px"
      alt="World Map"
      usemap="#map"
      @click="getPosition"
    />
    <div v-for="(semaforo, i) in semafori" :key="i + semaforo.title">
      <semafori-card
        :close="chooseNum"
        :semaforo="semaforo"
        :visible="isVisible(semaforo)"
        class="absolute position-text"
      ></semafori-card>
    </div>
    <map v-if="choose == -1" name="map">
      <area
        v-for="(semaforo, i) in semafori"
        :id="i"
        :key="i"
        alt=""
        class="num"
        :coords="semaforo.choords.x + ',' + semaforo.choords.y + ',40'"
        shape="circle"
        data-toggle="collapse"
        data-target="#collapseLIKES"
        data-text="Collapse"
        @click="chooseNum(semaforo.index)"
      />
    </map>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { IContentDocument } from '@nuxt/content/types/content'
import { Context } from '@nuxt/types'
import Point from '~/types/Point'

export default Vue.extend({
  name: 'Mappa',

  asyncData: async ({ $content }: Context) => {
    const u = await $content('mappa').fetch()

    const semafori = u.map((us: IContentDocument) => ({
      title: us.title,
      description: us.descriptionBody,
      consequences: us.consequences,
      choords: us.choords,
      index: us.index,
    }))

    return { semafori }
  },
  data() {
    return {
      choose: -1,
    }
  },
  methods: {
    getPosition(e: any) {
      console.log('x: ' + e.clientX + ' y: ' + e.clientY)
    },
    chooseNum(i: number): void {
      this.choose = this.choose === i ? -1 : i
    },
    isVisible(point: Point): Boolean {
      return this.choose === point.index
    },
  },
})
</script>

<style scoped>
area:hover {
  cursor: pointer;
}

area {
  background: red;
}

.position-text {
  overflow-y: auto;
  top: 44%;
  right: 5%;
  width: 58%;
  height: 54%;
}
</style>
