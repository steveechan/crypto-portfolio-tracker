<template>
<div class="app">
  <div class="entry">
  Token <select required v-model="token.name"> 
          <option v-for="coin in coins" :key="coin.item"> {{ coin.symbol }} </option>
        </select> <br>
  Price <input type="text" required v-model="token.price"> <br>
  Quantity <input type="text" required v-model="token.quantity"> <br>
  <button @click="addData"> Add </button>
  </div>
  <div class="portfolio">
    <table class="styled-table">
    <thead>
        <tr>
            <th v-if="image">  </th>
            <th>Token</th>
            <th>Price</th>
            <th>Quantity</th>
            <th>Total Investment</th>
            <th>Current Investment</th>
            <th>Current Price</th>
        </tr>
    </thead>
    <tbody>
        <tr class="active-row" v-for="(token, index) in tokens" :key="index">
            <td> <img :src="token.curr.image" class="tokenimage" /> </td>
            <td> {{ token.name.toUpperCase() }} </td>
            <td> {{ "$" + token.price }} </td>
            <td> {{ token.quantity }} </td>
            <td> {{ "$" + token.totalVal }} </td>
            <td> {{ "$" + parseFloat(token.curr.current_price) * token.quantity }} </td>
            <td> {{ "$" + token.curr.current_price }} <button @click="removeData"> - </button></td>
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
        totalIn: 0,
        curr: 0,
      },

      coins: [],

      image: false,
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
        totalIn: parseFloat(this.token.curr.current_price) * this.token.quantity,
        curr: this.coins.find(c=>c.symbol === this.token.name),
      })
      this.token.name = "";
      this.token.price = "";
      this.token.quantity = "";
      this.curr = "";
      this.image = true;
    },

    removeData(index) {
      this.tokens.splice(index, 1);
    }
  },
}
</script>

<style>

* {
  font-family: 'Poppins', sans-serif;
}

.portfolio {
  display: flex;
  align-items: center;
  justify-content: center;
}

.styled-table {
    border-collapse: collapse;
    margin: 25px 0;
    font-size: 0.9em;
    min-width: 400px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
}

.styled-table thead tr {
    background-color: #009879;
    color: #ffffff;
    text-align: left;
}

.styled-table th,
.styled-table td {
    padding: 12px 15px;
}

.styled-table tbody tr {
    border-bottom: 1px solid #dddddd;
}

.styled-table tbody tr:nth-of-type(even) {
    background-color: #f3f3f3;
}

.styled-table tbody tr:last-of-type {
    border-bottom: 2px solid #009879;
}

.styled-table tbody tr.active-row {
    font-weight: bold;
    color: #009879;
}

.tokenimage {
  width: 20px;
  height: 20px;
  display: block;
}

</style>
