<template>
  <div class="container">
    <div v-if="state == 'intro'">
      <h1>Comincia scegliendo gli interventi che credi utili alla tua città</h1>
    </div>
    <div v-if="state == 'game'">
      <p class="text-right">Amount: {{ amount }}</p>
      <table>
        <tr>
          <td></td>
          <td>N. intervento</td>
          <td>Intervento</td>
          <td>Costo</td>
        </tr>
        <tr
          v-for="(int, k) in interventi"
          :key="k"
          style="border-bottom: 1px solid black"
          @click="click(k)"
        >
          <td>
            <input
              type="checkbox"
              v-bind="checked(k)"
              :disabled="
                !allowWith(k) &&
                negativeBudget(k) &&
                !itemInChoosed(filtering(k).id)
              "
            />
          </td>
          <td>{{ filtering(k).name }}</td>
          <td class="text-left">
            {{ filtering(k).intervento }}
          </td>
          <td style="width: 150px">
            {{ filtering(k).costo }}<br />
            <div v-if="filtering(k).condition != null">
              <span v-if="filtering(k).condition.con != null">
                Con: {{ filtering(k).condition.con }}</span
              >
              <span v-if="filtering(k).condition.restituisci != null"
                >+{{ filtering(k).condition.restituisci }}</span
              >
            </div>
          </td>
        </tr>
      </table>
      <p class="p-7"><button @click="start">Avvia simulazione</button></p>
    </div>
    <div v-else-if="state == 'start game'">
      <h1 v-if="giri == 0">Simulazione Avviata</h1>
      <p v-html="evento"></p>
      <div v-if="end" class="links">
        <a href="/" class="button--grey mt-8" rel="noopener noreferrer"
          >Torna alla Home</a
        >
        <nuxt-link
          to="/gioco-simulazione"
          class="button--grey mt-8"
          rel="noopener noreferrer"
          >Rigioca</nuxt-link
        >
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { IContentDocument } from '@nuxt/content/types/content'
import { Context } from '@nuxt/types'
import Vue from 'vue'
import Intervento from '~/types/Intervento'

export default Vue.extend({
  asyncData: async ({ $content }: Context) => {
    const u = await $content('gioco-simulazione/interventi').fetch()
    const ev = await $content('gioco-simulazione/eventi').fetch()

    const interventi = u.map((us: IContentDocument) => ({
      id: us.id,
      name: us.name,
      intervento: us.intervento,
      costo: us.costo,
      condition: us.condition != null ? us.condition : null,
    }))

    const eventi = ev.map((us: IContentDocument) => ({
      id: us.id,
      evento: us.evento,
      danni: us.danni,
      condizioni: us.condizioni,
    }))

    return { interventi, eventi }
  },
  data() {
    return {
      state: '',
      millis: 1000,
      amount: 80000,
      choosed: [] as Array<number>,
      interventi: [] as Array<Intervento>,
      danni: 0,
      eventi: [] as Array<any>,
      giri: 0,
      evento: '',
      end: false,
    }
  },
  computed: {
    endedBudget(): boolean {
      return this.amount > 0
    },
  },
  mounted(): void {
    this.state = 'intro'
    setTimeout(() => {
      this.state = 'game'
    }, 5 * this.millis)
    // this.state = 'start game'
  },
  methods: {
    filtering(i: number) {
      return this.interventi.filter((item: Intervento) => {
        return item.id === i
      })[0]
    },
    click(i: number): void {
      const indexElem = this.choosed.findIndex((e) => e === i)
      if (indexElem !== -1) {
        if (this.filtering(i).condition) {
          if (
            this.filtering(i).condition != null ||
            (this.filtering(i).condition.con != null &&
              this.itemInChoosed(
                this.filterForName(this.filtering(i).condition.con).id
              ))
          ) {
            this.amount -= this.filtering(i).condition.restituisci
          }
        }
        this.choosed.splice(indexElem, 1)
        this.amount =
          this.amount + this.filtering(i).costo > 80000
            ? 80000
            : this.amount + this.filtering(i).costo
      } else {
        const elem = this.filtering(i)
        if (this.filtering(i).condition) {
          if (
            this.filtering(i).condition != null ||
            (this.filtering(i).condition.con != null &&
              this.itemInChoosed(
                this.filterForName(this.filtering(i).condition.con).id
              ))
          ) {
            this.amount += this.filtering(i).condition.restituisci
          }
        } else if (this.amount - elem.costo < 0) {
          alert('budget non sufficiente')
          return
        }
        this.amount -= elem.costo
        this.choosed.push(i)
      }
    },
    itemInChoosed(item: number): boolean {
      return this.choosed.findIndex((e) => e === item) !== -1
    },
    checked(item: number): any {
      return this.itemInChoosed(item) ? { [`checked`]: 'true' } : null
    },
    negativeBudget(index: number): boolean {
      return this.amount - this.filtering(index).costo < 0
    },
    filterForName(number: String): Intervento {
      return this.interventi.filter((i: Intervento) => i.name === number)[0]
    },
    allowWith(item: number): any {
      if (this.filtering(item).condition != null) {
        var filtered = this.filterForName(this.filtering(item).condition.con)
        if (filtered !== undefined) {
          if (this.itemInChoosed(filtered.id)) {
            return true
          }
        }
      }
      return false
    },
    start() {
      this.state = 'start game'
      this.startSimulation()
    },
    startSimulation() {
      if (this.danni < 15) {
        const index: number = Math.floor(Math.random() * this.eventi.length)
        this.evento = this.eventi[index].evento
        if (this.eventi[index].danni !== undefined) {
          this.danni += this.eventi[index].danni
        } else {
          this.eventi[index].condizioni.forEach((element: any) => {
            // console.log(element)
            let elementCounted: number = 0
            element.tra.forEach((e: any) => {
              const finded = this.filterForName(e.toString())
              if (finded !== undefined) {
                // console.log(finded.id)
                elementCounted++
              }
              // console.log(this.itemInChoosed(finded))
            })
            if (element.tipo === 'uguale') {
              if (element.quantità === elementCounted) {
                this.danni += element.danni
              }
            } else if (element.tipo === 'almeno') {
              if (element.quantità >= elementCounted) {
                this.danni += element.danni
              }
            } else if (element.tipo === 'minore') {
              if (element.quantità <= elementCounted) {
                this.danni += element.danni
              }
            }
          })
        }
        this.eventi.splice(index, 1)
        this.giri++
        setTimeout(() => {
          return this.startSimulation()
        }, 3000)
      } else {
        console.log(this.giri)
        if (this.giri > 12) {
          this.evento =
            'La tua città è sopravvissuta a ' +
            this.giri.toString() +
            ' eventi <br>' +
            ' Complimenti, i tuoi concittadini sono orgogliosi di te e del lavoro svolto per mantenere la città al sicuro...avrai sempre il loro appoggio!'
        } else if (this.giri > 10 && this.giri <= 12) {
          this.evento =
            'La tua città è sopravvissuta a ' +
            this.giri.toString() +
            ' eventi <br>' +
            'Bene, la tua città ha resistito a sufficienza, sicuramente la prossima volta raggiungerai il top!'
        } else if (this.giri > 7 && this.giri <= 10) {
          this.evento =
            'La tua città è sopravvissuta a ' +
            this.giri.toString() +
            ' eventi <br>' +
            'La città non ha resistito a tutto quello che è capitato, forse dovresti rivedere alcune scelte...hai qualcosa su cui ragionare per il futuro!'
        } else if (this.giri > 4 && this.giri <= 7) {
          this.evento =
            'La tua città è sopravvissuta a ' +
            this.giri.toString() +
            ' eventi <br>' +
            'La città era veramente fragile, sei sicuro di aver speso bene i tuoi fondi?'
        } else if (this.giri > 1 && this.giri <= 4) {
          this.evento =
            'La tua città è sopravvissuta a ' +
            this.giri.toString() +
            ' eventi <br>' +
            'Stai scherzando vero?'
        } else {
          console.log(this.giri)
        }
        this.end = true
      }
    },
  },
})
</script>
