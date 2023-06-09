<template>
  <div id="app" class="container-prod">
    <h1>Product List</h1>
    <input
      type="text"
      v-model="searchTerm"
      @input="handleSearch"
      class="form-control mb-40"
      placeholder="Search Products"
    />
    <table class="table">
      <thead>
        <tr>
          <th scope="col">Product title</th>
          <th scope="col">Price</th>
          <th scope="col">Category</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in products" :key="product.id">
          <td>
            {{ product.title }}
          </td>
          <td>
            {{ product.price }}
          </td>
          <td>
            {{ product.category }}
          </td>
        </tr>
      </tbody>
    </table>
    <div class="d-flex justify-content-end">
      <button
        @click="prevPage"
        :disabled="currentPage === 1"
        class="btn btn-primary mr-10"
      >
        Previous Page
      </button>
      <button
        @click="nextPage"
        :disabled="currentPage === totalPages"
        class="btn btn-primary"
      >
        Next Page
      </button>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";

export default {
  name: "ProductList",
  setup() {
    const products = ref([]);
    const currentPage = ref(1);
    const totalPages = ref(1);
    const searchTerm = ref("");

    const fetchProducts = async () => {
      try {
        fetch("https://dummyjson.com/products")
          .then((res) => res.json())
          .then((res) => {
            products.value = res.products.slice(0, 10);
            currentPage.value = 1;
            totalPages.value = Math.ceil(res.products.length / 10);
          });
      } catch (error) {
        console.error("Error fetching products:", error);
      }
    };

    const prevPage = async () => {
      try {
        fetch("https://dummyjson.com/products")
          .then((res) => res.json())
          .then((res) => {
            const startIndex = (currentPage.value - 2) * 10;
            const endIndex = startIndex + 10;
            products.value = res.products.slice(startIndex, endIndex);
            currentPage.value--;
          });
      } catch (error) {
        console.error("Error loading next page:", error);
      }
    };

    const nextPage = async () => {
      try {
        fetch("https://dummyjson.com/products")
          .then((res) => res.json())
          .then((res) => {
            const startIndex = currentPage.value * 10;
            const endIndex = startIndex + 10;
            products.value = res.products.slice(startIndex, endIndex);
            currentPage.value++;
          });
      } catch (error) {
        console.error("Error loading next page:", error);
      }
    };

    const handleSearch = () => {
      console.log("hehehhe", searchTerm.value.toLowerCase());
      fetch(
        `https://dummyjson.com/products/search?q=${searchTerm.value.toLowerCase()}`
      )
        .then((res) => res.json())
        .then((res) => {
          products.value = res.products.slice(0, 10);
        });
    };

    onMounted(fetchProducts);

    return {
      products,
      currentPage,
      totalPages,
      prevPage,
      nextPage,
      searchTerm,
      handleSearch,
    };
  },
};
</script>
