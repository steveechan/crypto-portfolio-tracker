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
      <td> Token </td>
      <ul class="list">
        <li v-for="token in tokens" :key="token.name"> {{ token.name }}  </li>
      </ul>
      <td> Shares </td>
      <ul class="list">
        <li v-for="token in tokens" :key="token.price"> {{ token.price }}  </li>
      </ul>
      <td> Quantity </td>
      <ul class="list">
        <li v-for="token in tokens" :key="token.quantity"> {{ token.quantity }}  </li>
      </ul>
  </div>
</div>
</template>

<script>

export default {
    name: "Add-Component",
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
      this.tokens.push(this.token)
      this.name = ""
    }
  }
}
</script>

<style>
.list {
  list-style: none;
}
</style>
