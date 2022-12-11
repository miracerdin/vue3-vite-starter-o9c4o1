<script setup>
import { ref, watch } from 'vue'
import Loading from './Loading.vue'
const searchTerm = ref('')
const listArray = ref([])
const debounce = ref(0)
const loading = ref(false)

const getResponse = async term => {
  loading.value = true
  const phones = await fetch(`https://dummyjson.com/products/search?q=${term}`).then(res => res.json())
  listArray.value = phones.products
  console.log(listArray.value)
  loading.value = false
}

const findProducts = async term => {
  if (!term) return (listArray.value = [])
  clearTimeout(debounce.value)
  debounce.value = setTimeout(() => {
    getResponse(term)
  }, 700)
}

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <Loading v-if="loading" />
    <ul class="max-h-46 overflow-auto ml-8">
      <h2 v-if="!listArray.length && searchTerm">No matches!</h2>
      <li v-for="(list, index) in listArray" key="index" class="list-none border-y-2 w-screen">
        <h3 class="text-4xl font-black text-gray-900 dark:text-white">{{ list.brand }}</h3>
        <img :src="list.thumbnail" alt="images" class="w-32 object-contain" />
        <div class="text-xl">Price:{{ list.price }}$</div>
      </li>
    </ul>
  </div>
</template>
