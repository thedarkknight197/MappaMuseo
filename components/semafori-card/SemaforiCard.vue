<template>
  <div
    v-if="visible"
    class="rounded-xl"
    style="overflow-y: auto; background: rgba(255, 255, 255, 0.75)"
  >
    <button
      class="rounded-xl border-red-700 hover:border-white hover:text-white hover:bg-red-700 p-3.5 m-3.5 border-2 text-red-700"
      @click="closeMe()"
    >
      X Chiudi
    </button>
    <div class="p-3.5">
      <h1 class="text-5xl font-bold mb-8">{{ semaforo.title }}</h1>
      <p
        class="text-3xl font-light mb-8"
        v-html="reduce(semaforo.description[section])"
      ></p>
      <div class="grid grid-cols-3 gap-4">
        <div>
          <button v-if="section > 0" class="text-left" @click="section--">
            <i class="fas fa-arrow-left text-5xl text-yellow-700"></i>
          </button>
        </div>
        <div>
          <p class="text-center">
            <nuxt-link to="/" class="text-5xl"
              ><i class="fas fa-home"></i> Home</nuxt-link
            >
          </p>
        </div>
        <div class="text-right">
          <button
            v-if="section < semaforo.description.length - 1"
            @click="section++"
          >
            <i class="fas fa-arrow-right text-5xl text-yellow-700"></i>
          </button>
        </div>
      </div>
    </div>
    <div v-if="section == semaforo.description.length - 1">
      <button
        class="rounded-xl border-red-700 hover:border-white hover:text-white hover:bg-red-700 p-3.5 m-3.5 border-2 text-red-700"
        @click="show = !show"
      >
        {{ !show ? 'Conseguenze' : 'Nascondi' }}
      </button>
      <div v-if="show == true">
        <div
          v-for="(conseguenze, k) in semaforo.consequences"
          :key="conseguenze.description + k"
          class="p-3.5"
        >
          <span
            class="text-3xl text-red-600"
            v-html="reduce(conseguenze)"
          ></span>
        </div>
        <button
          class="rounded-xl border-red-700 hover:border-white hover:text-white hover:bg-red-700 p-3.5 m-3.5 border-2 text-red-700"
          @click="closeMe()"
        >
          X Chiudi
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue, { PropType } from 'vue'
import Point from '~/types/Point'

export default Vue.extend({
  props: {
    semaforo: {
      required: true,
      type: Object as PropType<Point>,
    },
    visible: {
      required: true,
      type: Boolean,
    },
    close: {
      required: true,
      type: Function,
    },
  },
  data() {
    return {
      show: false,
      section: 0,
    }
  },
  mounted(): void {
    this.section = 0
    this.show = false
  },
  methods: {
    reduce(data: string): string {
      return data.replace(/["]+/g, '')
    },
    closeMe(): void {
      this.section = 0
      this.show = false
      this.close(-1)
    },
  },
})
</script>
