```vue
<script setup>
import ProductList from './components/ProductList.vue'
import Cart from './components/Cart.vue'
</script>

<template>
  <div class="app">

    <!-- Navigation Bar -->
    <header class="navbar">
      <div class="logo">
        🛒 ShopCart
      </div>

      <div class="nav-title">
        Shopping Cart
      </div>
    </header>

    <!-- Main Content -->
    <main class="container">

      <section class="hero">
        <div>
          <h1>Welcome to ShopCart</h1>
          <p>Browse our products and add your favourites to the cart.</p>
        </div>
      </section>

      <div class="shopping-layout">

        <!-- Products -->
        <section class="products-section">
          <ProductList />
        </section>

        <!-- Cart -->
        <aside class="cart-section">
          <Cart />
        </aside>

      </div>

    </main>

    <footer>
      <p>ShopCart © 2026 | Vue.js + Spring Boot + MongoDB</p>
    </footer>

  </div>
</template>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
  background: #f5f7fb;
  color: #1f2937;
}

.app {
  min-height: 100vh;
}

/* Navbar */

.navbar {
  height: 70px;
  background: #111827;
  color: white;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 50px;
}

.logo {
  font-size: 24px;
  font-weight: bold;
}

.nav-title {
  font-size: 16px;
  color: #d1d5db;
}

/* Main */

.container {
  max-width: 1250px;
  margin: auto;
  padding: 30px;
}

/* Hero */

.hero {
  background: linear-gradient(135deg, #4f46e5, #7c3aed);
  color: white;
  padding: 35px;
  border-radius: 18px;
  margin-bottom: 30px;
}

.hero h1 {
  margin: 0 0 10px;
  font-size: 32px;
}

.hero p {
  margin: 0;
  font-size: 16px;
  opacity: 0.9;
}

/* Layout */

.shopping-layout {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 30px;
}

.products-section,
.cart-section {
  background: white;
  border-radius: 16px;
  padding: 25px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.08);
}

/* Footer */

footer {
  text-align: center;
  padding: 30px;
  color: #6b7280;
}

/* Responsive */

@media (max-width: 900px) {
  .shopping-layout {
    grid-template-columns: 1fr;
  }

  .navbar {
    padding: 0 20px;
  }

  .container {
    padding: 20px;
  }
}
</style>
```
