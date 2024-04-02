<script setup>
  import {ref, onMounted, watch} from 'vue'
  import {db} from './data/guitarras'
  import Guitarra from './components/Guitarra.vue'
  import Header from './components/Header.vue'
  import Footer from './components/Footer.vue'

  const guitarras = ref([])
  const carrito = ref([])
  const guitarra = ref({})

  onMounted(() => {
    guitarras.value = db
    guitarra.value = db[3]
    carrito.value = localStorage.getItem('carrito') ? JSON.parse(localStorage.getItem('carrito')) : []
  })

  watch(carrito, () => {
    guardarLocalStorage()
  }, {
    deep: true 
  })
  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
  }

  const agregarCarrito = (guitarra) =>{
    const existeCarrito = carrito.value.find(producto => producto.id === guitarra.id)

    if (existeCarrito) {
      existeCarrito.cantidad++
    } else {
      guitarra.cantidad = 1
      carrito.value.push(guitarra)
    }
  }

  const incrementarCantidad = (id) => {
    const productoEncontrado = carrito.value.find(producto => producto.id === id)
    productoEncontrado.cantidad++
  }

  const decrementarCantidad = (id) => {
    const productoEncontrado = carrito.value.find(producto => producto.id === id)
    if(productoEncontrado.cantidad <= 1) return
    productoEncontrado.cantidad--
  }

  const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id)
  }

  const vaciarCarrito = () => {
    carrito.value = []
  }
</script>

<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad"
    @eliminar-producto="eliminarProducto"
    @agregar-carrito="agregarCarrito"
    @vaciar-carrito="vaciarCarrito"
  />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
    </div>
  </main>
  <Footer />
</template>

<style scoped></style>
