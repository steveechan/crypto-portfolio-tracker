<template>
<div class="app">
  <div v-if="showModal" class="modal-mask">
    <div class="entry">
    <span> Token </span> <input list="coinOptions" v-model="token.name"> 
    <datalist id="coinOptions">
            <option v-for="coin in coins" :key="coin.item"> {{ coin.name }} </option>
    </datalist> <br> <br>
    <span> Price </span> <input type="number" required v-model="token.price"> <br> <br>
    <span> Quantity </span> <input type="number" required v-model="token.quantity"> <br> <br>
    <button class="addDataButton" @click="addData"> <span> Add </span> </button>
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
        <tr class="active-row" v-for="(merge, index) in merged" :key="index">
            <td> <img :src="merge.image" class="tokenimage" /> </td>
            <td> {{  merge.symbol.toUpperCase() }} </td>
            <td> {{ "$" + merge.price }} </td>
            <td> {{ merge.quantity }} </td>
            <td> {{ "$" + merge.totalVal.toFixed(2) }} </td>
            <td> {{ "$" + (merge.current_price * merge.quantity).toFixed(2) }} </td>
            <td> {{ "$" + merge.current_price.toFixed(2) }} </td>
            <td :class="
            [
              merge.current_price * merge.quantity - merge.price * merge.quantity < 0 ? 'red' : '',
            ]"
            > ${{ (merge.current_price * merge.quantity - merge.price * merge.quantity).toFixed(2) }} </td>
            <td> <button @click="removeData(index)" class="minus"> - </button> </td>
        </tr>          
    </tbody>
    <tfoot>
      <tr>
      </tr>
    </tfoot>
    </table>
  </div>
</div>
</template>

<script>
export default {
  mounted() {
    this.fetchCoins();
    
    if(localStorage.tokens) {
        this.tokens = JSON.parse(localStorage.tokens);
    }
  },
  watch: {
    tokens: {
      handler(newTokens) {
      localStorage.tokens = JSON.stringify(newTokens);
      },
      deep: true
    }
  },
  data () {
    return {
      tokens: [],
      
      red: false,

      token: {
        name: "",
        price: "",
        quantity: "",
        totalVal: 0,
        curr: 0,
        profit: 0,
      },
      coins: [],
      image: true,
      showModal: false,
      showPlus: true,
    }
  },
  computed: {
    merged(){
    let m = this.tokens.map(t=>{
      let mt = this.coins.find(c=>c.name === t.curr.name);
      if(mt){
        return Object.assign(t, mt);
      }

      return t;
    });

    return m;
    },
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
      let x = this.coins.find(c=>c.name === this.token.name);
      this.tokens.push( {
        name: this.token.name,
        price: this.token.price,
        quantity: this.token.quantity,
        totalVal: parseFloat(this.token.price) * this.token.quantity,
        curr: x,
        profit: (parseFloat(x.current_price) * this.token.quantity) - (parseFloat(this.token.price) * this.token.quantity),
      })
      this.token.name = "";
      this.token.price = "";
      this.token.quantity = "";
      this.totalVal = 0;
      this.curr = 0;
      this.image = true;
      this.showPlus = true;
      this.showModal = false;
    },
    removeData(index) {
      this.tokens.splice(index, 1);
    },
  },
}
</script>

<style>
* {
  font-family: 'Poppins', sans-serif;
}

span {
  font-weight: bold;
}
button {
border: none;
}
.addDataButton span {
  position: relative; 
  z-index: 1;
}
.addDataButton {
  border: none;
  display: block;
  text-align: center;
  cursor: pointer;
  text-transform: uppercase;
  outline: none;
  overflow: hidden;
  position: relative;
  color: #fff;
  font-weight: 700;
  font-size: 15px;
  background-color: #222;
  padding: 17px 60px;
  margin: 0 auto;
  box-shadow: 0 5px 15px rgba(0,0,0,0.20);
}

.addDataButton:hover:after {
  -webkit-transform: translateX(-9%) translateY(-25%) rotate(45deg);
  transform: translateX(-9%) translateY(-25%) rotate(45deg);
}

.addDataButton:after {
  content: "";
  position: absolute;
  left: 0;
  top: 0;
  height: 490%;
  width: 140%;
  background: #009879;
  -webkit-transition: all .5s ease-in-out;
  transition: all .5s ease-in-out;
  -webkit-transform: translateX(-50%) translateY(-25%) rotate(45deg);
  transform: translateX(-98%) translateY(-25%) rotate(45deg);
}

input {
  float: right;
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
  box-shadow: 0 2px 8px #009879;
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

.red {
  color: red;
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