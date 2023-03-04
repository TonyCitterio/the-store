<template>
  <div>
    <header>
      <div>
        <h1>The Store</h1>
      </div>
      <div class="shoppingCart">
        <p class="totalItemsInCart">
          {{ totalItems }} {{ totalItems === 1 ? "vara" : "varor" }},
          {{ totalSum }}:-
        </p>
        <i class="fas fa-shopping-cart" @click="showCart = !showCart"></i>
        <div
          v-if="showCart"
          class="shoppingCartCard"
          ref="shoppingCartContainer"
        >
          <div class="shoppingCartHeading">
            <h1>Varukorg</h1>
          </div>
          <div
            class="shoppingCartContent"
            v-for="product in modelValue"
            :key="product.id"
            @click.stop
          >
            <div class="shoppingCartContentInfo">
              <img :src="product.image" alt="Product Image" />
              <p>{{ product.title }}</p>
            </div>
            <div class="shoppingCartContentAmount">
              <div>
                <button @click="incrementProduct(product)">+</button>
                <p>{{ product.amount }}</p>
                <button @click="decrementProduct(product)">-</button>
              </div>
              <div>
                <p>{{ `${product.price}:-` }}/st</p>
                <p>Summa: {{ `${product.price * product.amount}:-` }}</p>
              </div>
            </div>
          </div>
          <div class="shoppingCartContentCheckout">
            <p v-if="isCartEmpty">Din kundvagn är tom</p>
            <button v-if="!isCartEmpty" @click="confirmClearCart">Töm</button>
            <p v-if="!isCartEmpty">Att betala: {{ totalSum }}</p>
          </div>
        </div>
      </div>
    </header>
  </div>
</template>

<script>
export default {
  props: ["modelValue"],
  computed: {
    totalSum() {
      let sum = 0;

      for (const product of this.modelValue) {
        sum += product.price * product.amount;
      }

      return sum;
    },
    totalItems() {
      let total = 0;

      for (const product of this.modelValue) {
        total += product.amount;
      }

      return total;
    },
    totalSumByProduct() {
      const productMap = {};

      for (const product of this.modelValue) {
        if (!productMap[product.id]) {
          productMap[product.id] = {
            price: product.price,
            amount: product.amount,
          };
        } else {
          productMap[product.id].amount += product.amount;
        }
      }

      let sum = 0;

      for (const productId in productMap) {
        sum += productMap[productId].price * productMap[productId].amount;
      }

      return sum;
    },
    isCartEmpty() {
      return this.modelValue.length === 0;
    },
  },
  data() {
    return {
      showCart: false,
    };
  },
  mounted() {
    window.addEventListener("click", this.handleWindowClick);
  },
  beforeUnmount() {
    window.removeEventListener("click", this.handleWindowClick);
  },
  methods: {
    handleWindowClick(event) {
      const shoppingCartContainer = this.$refs.shoppingCartContainer;
      if (!shoppingCartContainer) {
        return;
      }
      const shoppingCartIcon = shoppingCartContainer.previousElementSibling;
      if (
        shoppingCartContainer.contains(event.target) ||
        shoppingCartIcon.contains(event.target)
      ) {
        return;
      }
      this.showCart = false;
    },
    incrementProduct(product) {
      const shoppingCart = this.modelValue;
      const productIndex = shoppingCart.findIndex(
        (item) => item.id === product.id
      );
      shoppingCart[productIndex].amount += 1;

      this.$emit("update:modelValue", shoppingCart);
    },
    decrementProduct(product) {
      const shoppingCart = this.modelValue;
      const productIndex = shoppingCart.findIndex(
        (item) => item.id === product.id
      );
      shoppingCart[productIndex].amount -= 1;

      if (shoppingCart[productIndex].amount < 1) {
        shoppingCart.splice(productIndex, 1);
      }

      this.$emit("update:modelValue", shoppingCart);
    },
    confirmClearCart() {
      if (window.confirm("Är du säker på att du vill tömma din kundvagn?")) {
        this.clearCart();
      }
    },
    clearCart() {
      this.$emit("update:modelValue", []);
    },
  },
};
</script>

<style scoped>
header {
  position: relative;
  padding: 2rem;
  display: flex;
  justify-content: space-between;
  background-color: #333333;
}
header h1 {
  font-size: 2rem;
  color: #8e7dbe;
  margin: 0;
}
.shoppingCart {
  display: flex;
  align-items: center;
}
.shoppingCart i {
  font-size: 1.35rem;
  margin-right: 0.5rem;
  color: #8e7dbe;
  cursor: pointer;
}
.totalItemsInCart {
  margin: 0 0.5rem;
  font-size: 0.9rem;
  font-weight: bold;
  color: #8e7dbe;
}
.shoppingCartCard {
  position: absolute;
  width: 100%;
  left: 0;
  top: 125px;
  box-sizing: border-box;
  background-color: #f5f5f5;
  box-shadow: rgba(0, 0, 0, 0.25) 0px 54px 55px,
    rgba(0, 0, 0, 0.12) 0px -12px 30px, rgba(0, 0, 0, 0.12) 0px 4px 6px,
    rgba(0, 0, 0, 0.17) 0px 12px 13px, rgba(0, 0, 0, 0.09) 0px -3px 5px;
}
.shoppingCartHeading {
  padding: 1rem 0.5rem;
  margin-bottom: 0.75rem;
  background-color: #333333;
}
.shoppingCartHeading h1 {
  font-size: 1.75rem;
}
.shoppingCartContent {
  padding: 0.5rem;
  margin: 0.5rem;
  background-color: white;
  box-shadow: rgba(0, 0, 0, 0.16) 0px 10px 36px 0px,
    rgba(0, 0, 0, 0.06) 0px 0px 0px 1px;
}
.shoppingCartContent img {
  margin-right: 0.75rem;
  width: 40px;
  height: 40px;
  object-fit: contain;
}
.shoppingCartContent p {
  margin: 0;
}
.shoppingCartContentInfo {
  display: flex;
  margin-bottom: 0.75rem;
}
.shoppingCartContentAmount {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.shoppingCartContentAmount > *:nth-child(1),
.shoppingCartContentAmount > *:nth-child(2) {
  display: flex;
  align-items: center;
}
.shoppingCartContentAmount button {
  height: 22px;
  width: 35px;
  border: none;
  border-radius: 0.2rem;
  background-color: #8e7dbe;
  color: white;
}
.shoppingCartContentAmount p {
  margin: 0 0.5rem;
}
.shoppingCartContentCheckout {
  padding: 0.25rem 1rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.shoppingCartContentCheckout button {
  height: 30px;
  width: 100px;
  background-color: #8e7dbe;
  border: none;
  color: white;
  border-radius: 0.2rem;
}
@media screen and (min-width: 450px) {
  .shoppingCartCard {
    width: 500px;
    right: 10px;
    left: unset;
  }
  header h1 {
    font-size: 3rem;
  }
  .totalItemsInCart {
    font-size: 1rem;
  }
  .shoppingCart i {
    font-size: 1.7rem;
  }
}
</style>
