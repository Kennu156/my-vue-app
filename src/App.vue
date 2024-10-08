<script setup>
import { onMounted, ref, computed } from 'vue';
import axios from 'axios';

const products = ref([]);
const selectedCategory = ref('all');
const categories = ref([]);

const fetchAllProducts = async () => {
  const response = await axios.get('https://fakestoreapi.com/products');
  products.value = response.data;
  getAllCategories(response.data);
  return response.data;
};

const getAllCategories = (products) => {
  const allCategories = products.map(product => product.category);
  categories.value = ['all', ...new Set(allCategories)];
};

const filteredProducts = computed(() => {
  if (selectedCategory.value === 'all') {
    return products.value;
  }
  return products.value.filter(product => product.category === selectedCategory.value);
});

const addToCart = (product) => {
  console.log(`Product added to cart: ${product.title}`);
};

onMounted(async () => await fetchAllProducts());
</script>

<template>
  <div class="container mx-auto p-8">
    <h1 class="text-4xl font-bold text-center mb-8">Welcome to Fake Store</h1>
    <div class="mb-8">
      <label for="category" class="mr-4 font-semibold">Sort by Category:</label>
      <select id="category" v-model="selectedCategory" class="p-2 border rounded-lg">
        <option v-for="category in categories" :key="category" :value="category">
          {{ category }}
        </option>
      </select>
    </div>
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-8">
      <article v-for="product in filteredProducts" :key="product.id" class="border p-4 rounded-lg shadow-lg transition-shadow duration-300">
        <img class="w-full h-48 object-contain mb-4 rounded" :src="product.image" :alt="product.title" />
        <h4 class="text-2xl font-semibold mb-2">{{ product.title }}</h4>
        <p class="text-gray-500">{{ product.category }}</p>
        <p class="text-xl font-bold mt-2 text-green-600">${{ product.price }}</p>
        <button class="mt-4 bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded transition duration-300" @click="addToCart(product)">Add to Cart</button>
      </article>
    </div>
  </div>
</template>
