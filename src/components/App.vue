<script>
import 'bootstrap'

export default {
  data: function () {
    return {
      rates: {
        receivable: [0, 1.0945, 1.1050, 1.1100, 1.1180, 1.1250, 1.1270, 1.1290, 1.1310, 1.1335, 1.1399, 1.1450, 1.1498, 1.1840, 1.1889, 1.1940, 1.1990, 1.2050, 1.2095],
        limit: [0, 0.08634079488350799, 0.09502262443438902, 0.09909909909909898, 0.10554561717352395, 0.11111111111111094, 0.11268855368234199, 0.11426040744021304, 0.11582670203359902, 0.11777679752977499, 0.12273006404070497, 0.12663755458515302, 0.13028352757001205, 0.15540540540540504, 0.15888636554798596, 0.16247906197654904, 0.16597164303586298, 0.170124481327801, 0.17321207110376202]

      },
      value: 1000,
      term: 1,
      limit: false,
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
      this.limit ? this.result = {
        willCharge: this.formatter(this.value),
        willReceive: this.formatter(this.value - (this.value * this.rates.limit[this.term])),
        portion: this.formatter(this.value / this.term)
      } : this.result = {
        willCharge: this.formatter(this.value * this.rates.receivable[this.term]),
        willReceive: this.formatter(this.value),
        portion: this.formatter(this.value * this.rates.receivable[this.term] / this.term)
      }
    },
    formatter: function (e) {
      return e.toLocaleString("pt-BR", {
        style: "currency",
        currency: "BRL"
      })
    },
    copy: function () {
      let e = `Simulador SertãoCred\nSerá cobrado no cartão: ${this.result.willCharge}.\nVocê receberá: ${this.result.willReceive}.\nSer${this.term > 1 ? "ão" : "á"} ${this.term} parcela${this.term > 1 ? "s" : ""} de ${this.result.portion}`;
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
        <input type="range" class="form-range" min="1" max="18" v-model="term" @change="process">
        <h2 class="text-center">{{ term }}</h2>
      </div>
      <div class="form-check form-switch">
        <input class="form-check-input" type="checkbox" role="switch" v-model="limit" @change="process">
        <label class="form-check-label">Calcular {{limit?"a partir do limite":"valor à receber"}}</label>
      </div>
      <div class="jumbotron">
        <h1 class="display-6">Detalhes da simulação</h1>
        <p class="lead">Será cobrado no cartão: R$: {{ result.willCharge }}</p>
        <p class="lead">Você receberá: R$: {{ result.willReceive }}</p>
        <hr class="my-4">
        <p>Ser{{ parseInt(term) === 1 ? "á" : "ão" }} {{ term }} parcela{{ parseInt(term) === 1 ? "" : "s" }} de R$:
          {{ result.portion }}</p>
      </div>
      <p class="lead">
        <a class="btn btn-outline-primary btn-lg" role="button" @click="copy">Copiar</a>
      </p>
    </div>
  </div>
</template>

<style scoped>

</style>
