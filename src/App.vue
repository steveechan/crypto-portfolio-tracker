<template>
<div class="app">
  <div class="entry">
  <button @click="fetchCoins">Click it or ticket</button>
  Token <input type="text" required v-model="token.name">
  Price <input type="text" required v-model="token.price">
  Quantity <input type="text" required v-model="token.quantity">
  <button @click="addData"> Add </button>
  </div>
  <div class="portfolio">
    <table class="style-table">
    <thead>
        <tr>
            <th>Token</th>
            <th>Price</th>
            <th>Quantity</th>
            <th>Total Value</th>
            <th>Current Price</th>
        </tr>
    </thead>
    <tbody>
        <tr v-for="token in tokens" :key="token.item">
            <td> {{ token.name }} </td>
            <td> {{ "$" + token.price }} </td>
            <td> {{ token.quantity }} </td>
            <td> {{ "$" + token.totalVal }} </td>
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
        totalVal: 0,
      },

      coins: [],
    }
  },

  setup() {

  },
  methods: {
    async fetchCoins() {
      const response = await fetch("https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false&price_change_percentage=1h");
      const data = await response.json();
      console.log("new coins", data);
      this.coins.push(data);
    },

    addData() {
      this.tokens.push( {
        name: this.token.name,
        price: this.token.price,
        quantity: this.token.quantity,
        totalVal: parseFloat(this.token.price) * this.token.quantity,
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
