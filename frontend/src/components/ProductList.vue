<script setup>
import { ref, onMounted } from 'vue'

const products = ref([])
const loading = ref(true)
const error = ref('')

const quantities = ref({})

const API_URL = 'http://localhost:8081/api'


// ===============================
// GET ALL PRODUCTS
// ===============================

const fetchProducts = async () => {

  try {

    const response = await fetch(`${API_URL}/products`)

    if (!response.ok) {
      throw new Error('Failed to fetch products')
    }

    products.value = await response.json()

    // Set default quantity to 1
    products.value.forEach(product => {

      if (!quantities.value[product.id]) {
        quantities.value[product.id] = 1
      }

    })

  } catch (err) {

    console.error(err)

    error.value =
      'Unable to load products. Make sure Spring Boot is running.'

  } finally {

    loading.value = false

  }
}


// ===============================
// ADD TO CART
// ===============================

const addToCart = async (product) => {

  const quantity =
    quantities.value[product.id] || 1

  console.log('Adding product:', product.name)
  console.log('Product ID:', product.id)
  console.log('Quantity:', quantity)

  try {

    const response = await fetch(
      `${API_URL}/cart/${product.id}?quantity=${quantity}`,
      {
        method: 'POST'
      }
    )

    const result = await response.text()

    console.log('Status:', response.status)
    console.log('Response:', result)


    // Backend rejected request
    if (!response.ok) {

      alert(
        `Could not add ${product.name} to cart.\n\n${result}`
      )

      return
    }


    // Successfully added
    alert(
      `${product.name} added to cart!`
    )


    // Tell Cart.vue to refresh
    window.dispatchEvent(
      new Event('cart-updated')
    )


    // Reload products so stock changes
    await fetchProducts()


    // Reset selected quantity
    quantities.value[product.id] = 1

  } catch (error) {

    console.error(
      'Add to cart error:',
      error
    )

    alert(
      'Cannot connect to Spring Boot.\n\n' +
      'Make sure the backend is running on port 8081.'
    )

  }

}
// ===============================
// PAGE LOAD
// ===============================

onMounted(() => {

  fetchProducts()

})
</script>

<template>

  <div>

    <div class="section-header">
      <div>
        <h2>🛍️ Products</h2>
        <p>Choose products to add to your cart</p>
      </div>

      <span class="product-count">
        {{ products.length }} Products
      </span>
    </div>

    <!-- Loading -->

    <div v-if="loading" class="message">
      Loading products...
    </div>

    <!-- Error -->

    <div v-else-if="error" class="error-message">
      {{ error }}
    </div>

    <!-- Products -->

    <div v-else class="product-grid">

      <div
        v-for="product in products"
        :key="product.id"
        class="product-card"
      >

        <div class="product-icon">
          🛍️
        </div>

        <h3>{{ product.name }}</h3>

        <p class="price">
          ₹{{ product.price.toLocaleString('en-IN') }}
        </p>

        <p class="stock">
          Available: {{ product.quantity }}
        </p>

        <div class="product-actions">

  <!-- Quantity selector -->

  <div class="quantity-box">

    <button
      @click="
        quantities[product.id] =
        Math.max(
          1,
          quantities[product.id] - 1
        )
      "
    >
      −
    </button>

    <span>
      {{ quantities[product.id] }}
    </span>

    <button
      :disabled="
        quantities[product.id] >= product.quantity
      "
      @click="
        quantities[product.id]++
      "
    >
      +
    </button>

  </div>


  <!-- ADD TO CART BUTTON -->

  <button
    class="add-button"
    :disabled="product.quantity === 0"
    @click="addToCart(product)"
  >
    Add to Cart
  </button>

</div>

      </div>

    </div>

  </div>

</template>

<style scoped>

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
}

.section-header h2 {
  margin: 0;
  font-size: 25px;
}

.section-header p {
  margin: 5px 0 0;
  color: #6b7280;
}

.product-count {
  background: #eef2ff;
  color: #4f46e5;
  padding: 8px 14px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: bold;
}

.product-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}

.product-card {
  border: 1px solid #e5e7eb;
  border-radius: 14px;
  padding: 20px;
  transition: 0.2s;
}

.product-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.08);
}

.product-icon {
  width: 55px;
  height: 55px;
  background: #eef2ff;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 27px;
  margin-bottom: 15px;
}

.product-card h3 {
  margin: 0 0 8px;
  font-size: 19px;
}

.price {
  font-size: 21px;
  font-weight: bold;
  color: #4f46e5;
  margin: 8px 0;
}

.stock {
  color: #6b7280;
  font-size: 14px;
}

.product-actions {
  display: flex;
  gap: 10px;
  margin-top: 18px;
}

.quantity-box {
  display: flex;
  align-items: center;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  overflow: hidden;
}

.quantity-box button {
  border: none;
  background: #f3f4f6;
  width: 30px;
  height: 36px;
  cursor: pointer;
  font-size: 18px;
}

.quantity-box span {
  width: 30px;
  text-align: center;
  font-weight: bold;
}

.add-button {
  flex: 1;
  border: none;
  border-radius: 8px;
  background: #4f46e5;
  color: white;
  cursor: pointer;
  font-weight: bold;
}

.add-button:hover {
  background: #4338ca;
}

.message {
  text-align: center;
  padding: 40px;
  color: #6b7280;
}

.error-message {
  padding: 20px;
  background: #fee2e2;
  color: #b91c1c;
  border-radius: 10px;
}

@media (max-width: 600px) {

  .product-grid {
    grid-template-columns: 1fr;
  }

  .section-header {
    align-items: flex-start;
    gap: 10px;
  }

}

</style>
```
