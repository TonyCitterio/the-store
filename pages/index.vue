<template>
  <NavBar v-model="shoppingCart" />
  <header>
    <h1>Våra Produkter</h1>
  </header>
  <div class="content">
    <div
      v-for="(product, index) in products"
      :key="'product-' + index"
      class="card"
    >
      <img :src="product.image" alt="Product Image" />
      <div class="info">
        <p>{{ product.title }}</p>
      </div>
      <div class="buy">
        <button @click="addToCart(product)">Köp</button>
        <p>{{ `${product.price}:-` }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
const { data: products } = await useFetch("https://fakestoreapi.com/products");
</script>

<script>
export default {
  data() {
    return {
      shoppingCart: [],
    };
  },
  mounted() {
    this.shoppingCart = JSON.parse(
      localStorage.getItem("shoppingCart") || "[]"
    );
  },
  watch: {
    shoppingCart: {
      handler(newValue) {
        localStorage.setItem("shoppingCart", JSON.stringify(newValue));
      },
      deep: true,
    },
  },
  methods: {
    addToCart(product) {
      let exists = false;

      for (const cartItem of this.shoppingCart) {
        if (cartItem.id === product.id) {
          cartItem.amount = cartItem.amount + 1;
          exists = true;
          break;
        }
      }
      if (!exists) {
        this.shoppingCart.push({
          ...product,
          amount: 1,
        });
      }
    },
  },
};
</script>
<style scoped>
header {
  display: flex;
  flex-direction: column;
  min-height: 12vh;
  justify-content: center;
  align-items: center;
}
.content {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  padding: 2rem 0rem;
  width: 80%;
  margin: auto;
  row-gap: 5rem;
  column-gap: 3rem;
}
.card {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 1rem;
  height: 300px;
  border-radius: 0.25rem;
  box-shadow: rgba(0, 0, 0, 0.16) 0px 10px 36px 0px,
    rgba(0, 0, 0, 0.06) 0px 0px 0px 1px;
}
.card img {
  width: 120px;
  height: 120px;
  object-fit: contain;
}
.info {
  width: 100%;
  height: 130px;
}
.info p {
  line-height: 1.2em;
}
.buy {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
}
.buy button {
  width: 100px;
  height: 35px;
  background-color: #8e7dbe;
  color: white;
  border: none;
  border-radius: 0.25rem;
  cursor: pointer;
}
.buy p {
  font-size: 1.2rem;
}
</style>
