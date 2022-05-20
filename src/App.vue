<template>
<div class="app">
  <div v-if="showModal" class="modal-mask">
    <div class="entry">
    Token <input list="coinOptions" v-model="token.name"> 
    <datalist id="coinOptions">
            <option v-for="coin in coins" :key="coin.item"> {{ coin.symbol }} </option>
    </datalist> <br>
    Price <input type="number" required v-model="token.price"> <br>
    Quantity <input type="number" required v-model="token.quantity"> <br>
    <button @click="addData"> Add </button>
    </div>
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
            <th>Profit/Loss</th>
            <th><button v-if="showPlus" @click="showAdd"> + </button></th>
        </tr>
    </thead>
    <tbody>
        <tr class="active-row" v-for="(token, index) in tokens" :key="index">
            <td> <img :src="token.curr.image" class="tokenimage" /> </td>
            <td> {{ token.name.toUpperCase() }} </td>
            <td> {{ "$" + token.price }} </td>
            <td> {{ token.quantity }} </td>
            <td> {{ "$" + token.totalVal.toFixed(2) }} </td>
            <td> {{ "$" + token.totalIn.toFixed(2) }} </td>
            <td> {{ "$" + token.curr.current_price }} </td>
            <td> {{ "$" + token.profit.toFixed(2) }}  </td>
            <td> <button @click="removeData" class="minus"> - </button> </td>
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
        profit: 0,
      },

      coins: [],

      image: false,

      showModal: false,
      showPlus: true,

    }
  },

  methods: {
    async fetchCoins() {
      const response = await fetch("https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false&price_change_percentage=1h");
      const data = await response.json();
      this.coins = this.coins.concat(data);
    },

    showAdd() {
      this.showModal = true;
    },

    addData() {
      let x = this.coins.find(c=>c.symbol === this.token.name);
      this.tokens.push( {
        name: this.token.name,
        price: this.token.price,
        quantity: this.token.quantity,
        totalVal: parseFloat(this.token.price) * this.token.quantity,
        totalIn: parseFloat(x.current_price) * this.token.quantity,
        curr: x,
        profit: (parseFloat(x.current_price) * this.token.quantity) - (parseFloat(this.token.price) * this.token.quantity),
      })
      this.token.name = "";
      this.token.price = "";
      this.token.quantity = "";
      this.totalVal = 0;
      this.totalIn = 0;
      this.curr = 0;
      this.image = true;
      this.showPlus = true;
      this.showModal = false;
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

.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
}
.entry {
  width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
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

.minus {
  float: right;
}

</style>
