<template>
<div class="app">
  <div class="entry">
  Token <input type="text" required v-model="token.name">
  Price <input type="text" required v-model="token.price">
  Quantity <input type="text" required v-model="token.quantity">
  <button @click="addData"> Add </button>
  {{ tokens }}
  </div>
  <div class="portfolio">
    <table class="styled-table">
    <thead>
        <tr>
            <th>Token</th>
            <th>Price</th>
            <th>Quantity</th>
            <th>Total Value</th>
        </tr>
    </thead>
    <tbody>
        <tr v-for="token in tokens" :key="token.item">
            <td> {{ token.name }} </td>
            <td> {{ token.price }} </td>
            <td> {{ token.quantity }} </td>
        </tr>
            
    </tbody>
</table>
  </div>
</div>
</template>

<script>

export default {
  data () {
    return {
      tokens: [],
      
      token: {
        name: "",
        price: "",
        quantity: "",
      }
    }
  },
  methods: {
    addData() {
      this.tokens.push( {
        name: this.token.name,
        price: this.token.price,
        quantity: this.token.quantity,
      })
      this.token.name = "";
      this.token.price = "";
      this.token.quantity = "";
    }
  }
}
</script>

<style>

</style>
