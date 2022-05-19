<template>
<div class="app">
  <div class="entry">
  Token <select required v-model="token.name"> 
          <option v-for="coin in coins" :key="coin.item"> {{ coin.symbol }} </option>
        </select>
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
        <tr v-for="token in tokens" :key="token.name">
            <td> {{ token.name.toUpperCase() }} </td>
            <td> {{ "$" + token.price }} </td>
            <td> {{ token.quantity }} </td>
            <td> {{ "$" + token.totalVal }} </td>
            <td> {{ token.curr.current_price }}</td>
        </tr>
            
    </tbody>
    </table>
  </div>
</div>
</template>

<script>

export default {
  mounted() {
    this.fetchCoins();
  },
  data () {
    return {
      tokens: [],
      
      token: {
        name: "",
        price: "",
        quantity: "",
        totalVal: 0,
        curr: 0,
      },

      coins: [],
    }
  },

  methods: {
    async fetchCoins() {
      const response = await fetch("https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false&price_change_percentage=1h");
      const data = await response.json();
      this.coins = this.coins.concat(data);
    },

    addData() {
      this.tokens.push( {
        name: this.token.name,
        price: this.token.price,
        quantity: this.token.quantity,
        totalVal: parseFloat(this.token.price) * this.token.quantity,
        curr: this.coins.find(c=>c.symbol === this.token.name),
      })
      console.log(this.curr);
      this.token.name = "";
      this.token.price = "";
      this.token.quantity = "";
      this.curr = "";
    }
  },
}
</script>

<style>
td {
  text-align: center;
}
</style>
