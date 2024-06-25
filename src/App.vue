<script setup>
import { ref, watch } from 'vue'

const products = ref([])
const searchTerm = ref('')

const isLoading = ref(false)

function debounce(ck) {
  let timer
  return () => {
    clearTimeout(timer)
    timer = setTimeout(() => {
      ck()
    }, 300)
  }
}

const createDebounce = debounce(async term => {
  try {
    if (term.length === 0) return (products.value = [])
    isLoading.value = true
    const apiResponseBody = await (await fetch(`https://dummyjson.com/products/search?q=${term}&limit=5`)).json()
    products.value = apiResponseBody.products
  } catch (error) {
    console.error(error)
    alert('Holy cow!')
  } finally {
    isLoading.value = false
  }
})

const findProducts = term => createDebounce(term)

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <p v-if="isLoading" class="text-lg text-center">loading...</p>
    <ul v-else-if="products.length > 0">
      <li v-for="product in products" :key="product.id">{{ product.title }} - ${{ product.price }}</li>
    </ul>
  </div>
</template>
