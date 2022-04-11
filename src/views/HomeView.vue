<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h2>Product count: {{ products.length }}</h2>
    <button v-on:click="createProduct()">Create a Product</button>
    <input type="text" v-model="newProduct.name" />
    Name
    <input type="text" v-model="newProduct.price" />
    Price
    <input type="text" v-model="newProduct.description" />
    Description
    <input type="type" v-model="newProduct.image_url" />
    Image URL
    <div v-for="product in products" v-bind:key="product.id">
      <h2>{{ product.name }}</h2>
      <img v-bind:src="product.image_url" v-bind:alt="product.name" />
      <h3>{{ product.description }}</h3>
      <h3>{{ product.price }}</h3>
      <div>
        <button v-on:click="showProduct(product)">More Info</button>
      </div>
    </div>
    <dialog id="product-details">
      <form method="dialog">
        <h1>Product Information</h1>
        <p>Name: {{ currentProduct.name }}</p>
        <p>Price: {{ currentProduct.price }}</p>
        <p>Description: {{ currentProduct.description }}</p>
        <p>Image: {{ currentProduct.image_url }}</p>

        <h1>Edit Product</h1>
        <p>
          Product Name:
          <input v-model="currentProduct.name" type="text" />
        </p>
        <p>
          Product Price:
          <input v-model="currentProduct.price" type="text" />
        </p>
        <p>
          Product Image:
          <input v-model="currentProduct.image_url" type="text" />
        </p>
        <p>
          Product Description:
          <input v-model="currentProduct.description" type="text" />
        </p>
        <button v-on:click="updateProduct()">Edit Product</button>
        <button v-on:click="deleteProduct(currentProduct)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<script>
// import { get } from "http";
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Hello",
      products: [],
      newProduct: {},
      currentProduct: {},
    };
  },
  created: function () {
    this.indexProducts();
  },
  methods: {
    indexProducts() {
      axios.get("http://localhost:3000/products").then((response) => {
        console.log(response.data);
        this.products = response.data;
      });
    },
    createProduct() {
      axios.post("http://localhost:3000/products", this.newProduct).then((response) => {
        this.products.push(response.data);
      });
    },
    showProduct(product) {
      axios.get(`http://localhost:3000/products/${product.id}`).then((response) => {
        this.currentProduct = response.data;
        document.querySelector("#product-details").showModal();
      });
    },
    updateProduct() {
      axios.patch(`http://localhost:3000/products/${this.currentProduct.id}`, this.currentProduct).then((response) => {
        console.log("It works", response);
        var index = this.products.indexOf(this.currentProduct);
        this.products.splice(index, 1);
        this.products.push(response.data);
      });
    },
    deleteProduct(product) {
      axios.delete(`http://localhost:3000/products/${product.id}`).then((response) => {
        console.log(response);
        var index = this.products.indexOf(product);
        this.products.splice(index, 1);
      });
    },
  },
};
</script>

<style></style>
