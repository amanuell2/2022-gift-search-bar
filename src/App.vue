<script setup>
import { ref, watch } from 'vue'
import { debounce } from 'debounce'

const isLoading = ref(false)
const searchTerm = ref('')
const products = ref([])

const findProducts = debounce(async term => {
  try {
    if (term.length === 0) return (products.value = [])
    isLoading.value = true
    const apiResponseBody = await (
      await fetch(`https://dummyjson.com/products/search?q=${term}&limit=5`)
    ).json()
    products.value = apiResponseBody.products
  } catch (error) {
    alert("Error finding products")
  }
  finally {
    isLoading.value = false
  }

}, 300)



watch(searchTerm, newTerm => findProducts(newTerm))

</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..."
      :disabled="isLoading" />
    <p v-if="isLoading" class="text-lg text-center">Please wait...</p>
    <ul class="list-disc" v-else-if="!isLoading && products.length > 0">
      <li v-for="(product, index) in products" :key="index">{{ product.title }} </li>
    </ul>
  </div>
</template>
