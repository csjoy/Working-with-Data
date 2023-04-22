<script setup lang="ts">

</script>

<template>
  <input type="text" v-model="name">
  <div>
    <span>{{ error }}</span>
  </div>
  <div>{{ formalName }}</div>
  <div>
    <p>{{ fullName }}</p>
    <p>{{ totalEntries }}</p>
    <p>{{ featuredEntries }}</p>
  </div>
  <input type="text" v-model="firstName" placeholder="First name">
  <input type="text" v-model="lastName" placeholder="Last name">
  <h3>{{ fullname }}</h3>

  <div class="container">
    <input type="text" v-model="incrementOne" placeholder="enter here">
    <h3>Get input: {{ incrementOne }}</h3>
    <h5>Set division: {{ divideByTwo }}</h5>
  </div>

  <div class="container">
    <h2 class="text-4xl font-bold my-4">Shop Watcher</h2>
    <div>
      Black Friday sale
      <del>Was {{ oldDiscount }}%</del>
      <strong> Now {{ discount }}% OFF</strong>
    </div>
    <button class="my-4 px-5 py-2.5 rounded-lg bg-rose-500 text-white" @click="updateDiscount">Increase Discount!</button>
  </div>

  <div class="container">
    <h2 class="text-4xl font-bold my-4">Deep Watcher</h2>
    <div>
      <h4>{{ product.label }}</h4>
      <h5>${{ product.price }} (${{ discounts }} Off)</h5>
    </div>
    <button class="my-4 px-5 py-2.5 rounded-lg bg-rose-500 text-white" @click="updatePrice">Reduce Price!</button>
  </div>

  <div class="container">
    <h2 class="text-4xl font-bold my-4">Async fetch</h2>
    <button class="my-4 px-5 py-2.5 rounded-lg bg-rose-500" @click="fetchAdvice()">{{ loading ? "Loading..." : "Learn something profound" }}</button>
    <blockquote class="bg-gray-200 p-8 border-l-8 border-green-600" v-if="fetchResponse && !loading">{{ fetchResponse }}</blockquote>
  </div>

  <div class="container">
    <button class="my-4 px-5 py-2.5 rounded-lg bg-rose-500" @click="getNewName()">Click to generate name</button>
    <p v-if="author">{{ author }}</p>
  </div>
</template>

<script lang="ts">

export default {
  data() {
    return {
      name: "",
      title: "Mr.",
      surname: "Smith",
      post: {
        fields: {
          author: {
            firstName: "John",
            lastName: "Doe",
          },
          entries: [
            {
              title: "Entry 1",
              content: "Entry 1's content",
              featured: true,
            },
            {
              title: "Entry 2",
              content: "Entry 2's content",
              featured: false,
            }
          ]
        }
      },
      firstName: "",
      lastName: "",
      count: -1,
      divideByTwo: 0,
      oldDiscount: 0,
      discount: 5,
      organization: {
        name: "ABC",
        employees: [
          "Jack", "Jill"
        ]
      },
      discounts: 0,
      product: {
        price: 25,
        label: "Blue juice"
      },
      fetchResponse: "",
      loading: false,
      data: {},
      author: "",
    }
  },
  watch: {
    discount(newValue, oldValue) {
      this.oldDiscount = oldValue
      console.log(newValue)
    },
    'product.price'(newValue, oldValue) {
      this.discounts++
      console.log(newValue, oldValue)
    },
    quote() {
      return this.fetchResponse ? this.fetchResponse : null
    },
    data: function (newValue, oldValue) {
      this.author = newValue.first
      alert(`Name changed from ${oldValue.first} to ${newValue.first}`)
    }
  },
  methods: {
    updateDiscount() {
      this.discount = this.discount + 5
    },
    updatePrice() {
      if (this.product.price < 1) return
      this.product.price--
    },
    async fetchAdvice() {
      this.loading = true
      try {
        return await fetch("https://api.adviceslip.com/advice").then(response => response.json()).then(data => this.fetchResponse = data.slip.advice);
      } catch (error) {
        console.log(error)
      } finally {
        setTimeout(() => { this.loading = false }, 1000)
      }
    },
    async getNewName() {
      await fetch("https://randomuser.me/api").then(response => response.json()).then(data => this.data = data.results[0].name)
    }
  },
  computed: {
    incrementOne: {
      get() {
        return this.count + 1
      },
      set(value: number) {
        this.count = value - 1
        this.divideByTwo = value / 2
      },
    },
    error(): string {
      return this.name ? "" : "Name is required"
    },
    formalName() {
      return `${this.title} ${this.surname}`
    },
    fullName() {
      const { firstName, lastName } = this.post.fields.author
      return `${firstName} ${lastName}`
    },
    totalEntries() {
      return this.post.fields.entries.length
    },
    featuredEntries() {
      const { entries } = this.post.fields
      return entries.filter(entry => !!entry.featured)
    },
    fullname() {
      return `${this.firstName} ${this.lastName}`
    }
  }
}
</script>