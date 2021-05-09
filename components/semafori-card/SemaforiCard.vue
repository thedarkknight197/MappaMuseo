<template>
<!-- h-3/5 -->
    <div v-if="visible" class="bg-gray-50 rounded-xl" style="overflow-y: auto">
        <button @click="close()" class="rounded-xl border-red-700 hover:border-white hover:text-white hover:bg-red-700 p-3.5 m-3.5 border-2 text-red-700"> X Chiudi</button>
        <div class="p-3.5 ">
            <h1 class="text-7xl mb-8"> {{semaforo.title}} </h1>
            <p class="text-xl font-light  mb-8" v-html="reduce(semaforo.description[section])"></p>
            <div class="grid grid-cols-3 gap-4">
                <div>
                    <button class="text-left" v-if="section > 0" @click="section--">  back </button>
                </div>
                <div>
                    <p class="text-center">
                        <nuxt-link to="/">Home</nuxt-link>
                    </p>
                </div>
                <div class="text-right">
                    <button v-if="section < semaforo.description.length - 1" @click="section++"> ></button>
                </div>
            </div>
        </div>
        <div v-if="section == semaforo.description.length - 1">
        <button @click="show = !show" class="rounded-xl border-red-700 hover:border-white hover:text-white hover:bg-red-700 p-3.5 m-3.5 border-2 text-red-700">{{!show? 'Conseguenze' : 'Chiudi'}}</button>
            <div v-if="show==true">
                <div v-for="(conseguenze, k) in semaforo.consequences" :key="conseguenze.description + k" class="p-3.5">
                    <span class="text-3xl text-red-600" v-html="reduce(conseguenze)"></span>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import Vue, {PropType} from 'vue'
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
    data(){
        return {
            show: false,
            section: 0,
        }
    },
    mounted(): void{
        this.show = false;
        this.section = 0;
    },
    methods: {
      reduce( data: string) : string {
          console.log(data);
          return data.replace(/["]+/g, '')
      },
  }
})
</script>