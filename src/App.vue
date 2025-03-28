<script setup>
  import { ref, reactive, onMounted, watch } from 'vue'
  import { db } from './data/guitarras'
  import Guitarra from './components/Guitarra.vue'
  import Header from './components/Header.vue'
  import Footer from './components/Footer.vue'

  const guitarras = ref([])
  const carrito = ref([])
  const guitarra = ref({})

  watch(carrito, () => {
   guardarLocalStorage()
  }, {
    deep: true
  })

  onMounted(() => {
    guitarras.value = db;
    guitarra.value = db[3];

    const carritoStorage = localStorage.getItem('carrito')

    if (carritoStorage) {
      carrito.value = JSON.parse(carritoStorage)
    }
  })
  
  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
  }

  const agregarCarrito = (guitarra) => {
    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)

    if (existeCarrito >= 0) {
      carrito.value[existeCarrito].cantidad++
    } else {
      guitarra.cantidad = 1
      carrito.value.push(guitarra)
    }

    guardarLocalStorage()
  }

  const decrementarCantidad = producto => producto.cantidad > 1 ? producto.cantidad-- : 0
  const incrementarCantidad = producto => producto.cantidad < 5 ? producto.cantidad++ : 0
  const eliminarProducto = guitarra => carrito.value = carrito.value.filter(producto => producto !== guitarra)
  const vaciarCarrito = () => carrito.value = []
</script>

<template>
  <Header 
    :carrito="carrito"
    :guitarra="guitarra"
    @agregar-carrito="agregarCarrito"
    @decrementar-cantidad="decrementarCantidad"
    @incrementar-cantidad="incrementarCantidad"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
  />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra 
        v-for="guitarra in guitarras"
        v-bind:guitarra="guitarra"
        v-on:agregar-carrito="agregarCarrito"
      />
    </div>
  </main>

  <Footer />
</template>