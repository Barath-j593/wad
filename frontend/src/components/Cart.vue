```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const cartItems = ref([])
const total = ref(0)
const loading = ref(true)

const API_URL = 'http://localhost:8081/api'

/* Get cart items */

const fetchCart = async () => {

  try {

    const response = await fetch(`${API_URL}/cart`)

    if (!response.ok) {
      throw new Error('Failed to fetch cart')
    }

    cartItems.value = await response.json()

  } catch (error) {

    console.error('Error loading cart:', error)

  } finally {

    loading.value = false

  }

  calculateTotal()
}

/* Get total from backend */

const calculateTotal = async () => {

  try {

    const response = await fetch(`${API_URL}/cart/total`)

    if (!response.ok) {
      throw new Error('Failed to calculate total')
    }

    total.value = await response.json()

  } catch (error) {

    console.error('Error calculating total:', error)

  }
}

/* Update quantity */

const updateQuantity = async (item, newQuantity) => {

  if (newQuantity < 1) {
    return
  }

  try {

    const response = await fetch(
      `${API_URL}/cart/${item.id}?quantity=${newQuantity}`,
      {
        method: 'PUT'
      }
    )

    if (!response.ok) {
      throw new Error('Failed to update quantity')
    }

    await fetchCart()

  } catch (error) {

    alert('Could not update quantity.')

  }
}

/* Remove item */

const removeItem = async (item) => {

  try {

    const response = await fetch(
      `${API_URL}/cart/${item.id}`,
      {
        method: 'DELETE'
      }
    )

    if (!response.ok) {
      throw new Error('Failed to remove item')
    }

    await fetchCart()

  } catch (error) {

    alert('Could not remove item.')

  }
}

/* Refresh cart when product is added */

const refreshCart = () => {
  fetchCart()
}

onMounted(() => {

  fetchCart()

  window.addEventListener(
    'cart-updated',
    refreshCart
  )

})

onUnmounted(() => {

  window.removeEventListener(
    'cart-updated',
    refreshCart
  )

})
</script>

<template>

  <div class="cart">

    <!-- Cart Header -->

    <div class="cart-header">

      <div>
        <h2>🛒 Your Cart</h2>
        <p>{{ cartItems.length }} item(s)</p>
      </div>

    </div>

    <!-- Loading -->

    <div
      v-if="loading"
      class="empty-cart"
    >
      Loading cart...
    </div>

    <!-- Empty Cart -->

    <div
      v-else-if="cartItems.length === 0"
      class="empty-cart"
    >

      <div class="empty-icon">
        🛒
      </div>

      <h3>Your cart is empty</h3>

      <p>
        Add some products to get started.
      </p>

    </div>

    <!-- Cart Items -->

    <div v-else>

      <div
        v-for="item in cartItems"
        :key="item.id"
        class="cart-item"
      >

        <div class="item-icon">
          📦
        </div>

        <div class="item-details">

          <h3>
            {{ item.productName }}
          </h3>

          <p>
            ₹{{ item.price.toLocaleString('en-IN') }}
          </p>

          <div class="quantity-controls">

            <button
              @click="
                updateQuantity(
                  item,
                  item.quantity - 1
                )
              "
            >
              −
            </button>

            <span>
              {{ item.quantity }}
            </span>

            <button
              @click="
                updateQuantity(
                  item,
                  item.quantity + 1
                )
              "
            >
              +
            </button>

          </div>

        </div>

        <div class="item-right">

          <strong>
            ₹{{
              (item.price * item.quantity)
                .toLocaleString('en-IN')
            }}
          </strong>

          <button
            class="remove-button"
            @click="removeItem(item)"
          >
            Remove
          </button>

        </div>

      </div>

      <!-- Total -->

      <div class="cart-summary">

        <div class="summary-row">

          <span>Subtotal</span>

          <strong>
            ₹{{ total.toLocaleString('en-IN') }}
          </strong>

        </div>

        <div class="summary-row">

          <span>Delivery</span>

          <span class="free">
            FREE
          </span>

        </div>

        <hr>

        <div class="total-row">

          <span>Total</span>

          <strong>
            ₹{{ total.toLocaleString('en-IN') }}
          </strong>

        </div>

        <button class="checkout-button">
          Proceed to Checkout
        </button>

      </div>

    </div>

  </div>

</template>

<style scoped>

.cart-header {
  border-bottom: 1px solid #e5e7eb;
  padding-bottom: 18px;
  margin-bottom: 20px;
}

.cart-header h2 {
  margin: 0;
  font-size: 25px;
}

.cart-header p {
  margin: 5px 0 0;
  color: #6b7280;
}

/* Empty */

.empty-cart {
  text-align: center;
  padding: 50px 20px;
  color: #6b7280;
}

.empty-icon {
  font-size: 50px;
  margin-bottom: 15px;
}

.empty-cart h3 {
  color: #374151;
  margin-bottom: 5px;
}

/* Cart item */

.cart-item {
  display: flex;
  gap: 12px;
  padding: 18px 0;
  border-bottom: 1px solid #e5e7eb;
}

.item-icon {
  width: 45px;
  height: 45px;
  background: #f3f4f6;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.item-details {
  flex: 1;
}

.item-details h3 {
  margin: 0 0 5px;
  font-size: 16px;
}

.item-details p {
  margin: 0 0 10px;
  color: #6b7280;
  font-size: 14px;
}

.item-right {
  text-align: right;
}

.item-right strong {
  display: block;
  margin-bottom: 10px;
}

/* Quantity */

.quantity-controls {
  display: flex;
  align-items: center;
  width: fit-content;
  border: 1px solid #d1d5db;
  border-radius: 7px;
  overflow: hidden;
}

.quantity-controls button {
  border: none;
  background: #f3f4f6;
  width: 28px;
  height: 28px;
  cursor: pointer;
  font-size: 17px;
}

.quantity-controls span {
  width: 30px;
  text-align: center;
  font-size: 14px;
}

/* Remove */

.remove-button {
  border: none;
  background: none;
  color: #dc2626;
  cursor: pointer;
  font-size: 13px;
}

.remove-button:hover {
  text-decoration: underline;
}

/* Summary */

.cart-summary {
  margin-top: 20px;
}

.summary-row {
  display: flex;
  justify-content: space-between;
  margin-bottom: 12px;
  color: #6b7280;
}

.summary-row strong {
  color: #111827;
}

.free {
  color: #16a34a;
  font-weight: bold;
}

hr {
  border: none;
  border-top: 1px solid #e5e7eb;
  margin: 18px 0;
}

.total-row {
  display: flex;
  justify-content: space-between;
  font-size: 20px;
  margin-bottom: 20px;
}

.total-row strong {
  color: #4f46e5;
}

.checkout-button {
  width: 100%;
  border: none;
  padding: 14px;
  border-radius: 9px;
  background: #111827;
  color: white;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
}

.checkout-button:hover {
  background: #374151;
}

</style>
```
