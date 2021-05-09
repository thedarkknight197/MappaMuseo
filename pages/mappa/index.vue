<template>
    <div class="relative">
        <nuxt-link to="/" 
            class="absolute left-0 top-0 rounded-xl border-red-700 hover:border-white hover:text-white hover:bg-red-700 p-3.5 m-3.5 border-2 text-red-700"
            > Torna a Home</nuxt-link>
        <img src="~/assets/WorldMap1.jpg" width="1920px" height="1080px" alt="World Map" usemap="#map" @click="getPosition">
        <div v-for="(semaforo, i) in semafori" :key="i+semaforo.title">
            <semafori-card :close="chooseNum"
                :semaforo="semaforo"
                :visible='isVisible(i)'
                class="absolute w-3/4 left-48 top-1/4"
            ></semafori-card>
        </div> 
        <map name="map">
            <area alt="" 
                class="num" v-for="(semaforo, i) in semafori" 
                :key="i" 
                :id="i" 
                :coords="semaforo.choords.x+','+semaforo.choords.y+',40'" 
                @click="chooseNum(i)" 
                shape="circle" 
                data-toggle="collapse" 
                data-target="#collapseLIKES" data-text="Collapse"
            />
        </map>
    </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { IContentDocument } from '@nuxt/content/types/content'
import { Context } from '@nuxt/types'

export default Vue.extend({
    name: 'Mappa',

  asyncData: async ({ $content }: Context) => {
    const u = await $content('mappa').fetch()

    const semafori = u.map((us: IContentDocument) => (
        {
        title: us.title,
        description: us.description,
        consequences: us.consequences,
        choords: us.choords
      }))
    return { semafori }
  },
  data(){
      return {
          choose: -1,
      }
  },
  methods: {
    getPosition(e : any){
        console.log('hover')
        console.log('x: ' + e.clientX + ' y: ' + e.clientY)
    },
    chooseNum(i: number): void{
        this.choose =  this.choose== i ? -1 : i;
    },
    isVisible(i: number): Boolean{
        return this.choose == i;
    },
  }
})

</script>

<style>
area:hover{
    cursor: pointer;
}

area{
    background: red;
}
</style>