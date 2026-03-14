<script>
import 'bootstrap'

export default {
  data: function () {
    return {
      rates: {
        visaMaster: {
          receivable: [0, 1.0945, 1.1050, 1.1100, 1.1180, 1.1250, 1.1270, 1.1290, 1.1310, 1.1335, 1.1399, 1.1450, 1.1498, 1.1840, 1.1889, 1.1940, 1.1990, 1.2050, 1.2095, 1.2495, 1.2568, 1.2639],
          limit: [0, 0.08634079488350799, 0.09502262443438902, 0.09909909909909898, 0.10554561717352395, 0.11111111111111094, 0.11268855368234199, 0.11426040744021304, 0.11582670203359902, 0.11777679752977499, 0.12273006404070497, 0.12663755458515302, 0.13028352757001205, 0.15540540540540504, 0.15888636554798596, 0.16247906197654904, 0.16597164303586298, 0.170124481327801, 0.17321207110376202, 0.19967987194878, 0.20432845321451298, 0.20879816441174104]
        },
        eloAmex: {
          receivable: [0, 1.0918, 1.1098, 1.1184, 1.1269, 1.1356, 1.1442, 1.1535, 1.1620, 1.1710, 1.1826, 1.1890, 1.1993, 1.2070, 1.2160, 1.2251, 1.2342, 1.2435, 1.2506],
          limit: [0, 0.08408133357757797, 0.09893674535952401, 0.10586552217453504, 0.11260981453545105, 0.11940824233885206, 0.12602691837091395, 0.13307325530992598, 0.13941480206540402, 0.14602903501280995, 0.15440554709961096, 0.158957106812447, 0.16618027182523099, 0.17149958574979296, 0.17763157894736803, 0.18374010284874698, 0.18975854804731807, 0.195818254925613, 0.200383815768431]
        }
      },
      value: 1000,
      term: 1,
      limit: false,
      cardFlag: false,
      result: {
        willCharge: 0,
        willReceive: 0,
        portion: 0
      }
    }
  },
  mounted() {
    this.process()
  },
  methods: {
    process: function () {
      const selectedRates = this.cardFlag
          ? this.rates.eloAmex
          : this.rates.visaMaster

      if (this.limit) {
        this.result = {
          willCharge: this.formatter(this.value),
          willReceive: this.formatter(this.value - (this.value * selectedRates.limit[this.term])),
          portion: this.formatter(this.value / this.term)
        }
      } else {
        this.result = {
          willCharge: this.formatter(this.value * selectedRates.receivable[this.term]),
          willReceive: this.formatter(this.value),
          portion: this.formatter((this.value * selectedRates.receivable[this.term]) / this.term)
        }
      }
    },
    formatter: function (e) {
      return Number(e).toLocaleString("pt-BR", {
        style: "currency",
        currency: "BRL"
      })
    },
    copy: function () {
      let flag = this.cardFlag ? 'Elo e Amex' : 'MasterCard e Visa'
      let e = `Simulador SertãoCred
Bandeiras consideradas: ${flag}
Será cobrado no cartão: ${this.result.willCharge}.
Você receberá: ${this.result.willReceive}.
Ser${this.term > 1 ? "ão" : "á"} ${this.term} parcela${this.term > 1 ? "s" : ""} de ${this.result.portion}`

      navigator.clipboard.writeText(e)
    }
  }
}
</script>

<template>
  <div class="text-bg-dark">
    <div class="col-6 offset-3">
      <div class="col-4 offset-4 pt-5">
        <img alt="Logo" class="img-fluid" src="../img/logo.png">
      </div>

      <div class="mb-3">
        <label for="exampleFormControlInput1" class="form-label">Valor: </label>
        <input type="number" class="form-control" v-model="value" @change="process">
      </div>

      <div class="col-12">
        <label class="form-label">Número de parcelas: </label>
        <input type="range" class="form-range" min="1" max="21" v-model="term" @change="process">
        <h2 class="text-center">{{ term }}</h2>
      </div>

      <div class="form-check form-switch">
        <input class="form-check-input" type="checkbox" role="switch" v-model="limit" @change="process">
        <label class="form-check-label">
          Calcular {{ limit ? "a partir do limite" : "valor à receber" }}
        </label>
      </div>

      <div class="form-check form-switch">
        <input class="form-check-input" type="checkbox" role="switch" v-model="cardFlag" @change="process">
        <label class="form-check-label">
          Bandeira do cartão: {{ cardFlag ? "Elo e Amex" : "MasterCard e Visa" }}
        </label>
      </div>

      <div class="jumbotron">
        <h1 class="display-6">Detalhes da simulação</h1>
        <p class="lead">Bandeiras consideradas: {{ cardFlag ? "Elo e Amex" : "MasterCard e Visa" }}</p>
        <p class="lead">Será cobrado no cartão: {{ result.willCharge }}</p>
        <p class="lead">Você receberá: {{ result.willReceive }}</p>
        <hr class="my-4">
        <p>
          Ser{{ parseInt(term) === 1 ? "á" : "ão" }}
          {{ term }}
          parcela{{ parseInt(term) === 1 ? "" : "s" }}
          de {{ result.portion }}
        </p>
      </div>

      <p class="lead">
        <a class="btn btn-outline-primary btn-lg" role="button" @click="copy">Copiar</a>
      </p>
    </div>
  </div>
</template>

<style scoped>
</style>