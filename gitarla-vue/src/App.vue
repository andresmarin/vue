<script setup>
import { ref, reactive, onMounted, watch } from 'vue'
import {db} from './data/guitarras'
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'



/* Example using reactive
 
const state = reactive({
    guitarra: db
})
    */

const guitarras = ref([])
const carrito = ref([])
const guitarra = ref({})

watch(carrito, () => {
        guardarLocalStorage()
    }, 
    {
        deep: true
    })

onMounted(() => {
    guitarras.value = db
    guitarra.value = guitarras.value[3]

    const carritoStorage = localStorage.getItem('carrito')
    if(carritoStorage){
        carrito.value = JSON.parse(carritoStorage)
    }
})

const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

const agregarCarrito = (guitarra) => {
    const existe = carrito.value.findIndex(item => item.id === guitarra.id)

    if(existe >= 0){
       carrito.value[existe].cantidad++
    }
    else{
        guitarra.cantidad = 1
        carrito.value.push(guitarra)
    }
}

const incrementarCantidad = (id) => {
    const existe = carrito.value.findIndex(item => item.id === id)

    if(existe >= 0){
       carrito.value[existe].cantidad++
       calcularTotal()
    }
}

const decrementarCantidad = (id) => {
    const existe = carrito.value.findIndex(item => item.id === id)

    if(existe >= 0){
        if(carrito.value[existe].cantidad > 1){
            carrito.value[existe].cantidad--
        }
        else{
            carrito.value.splice(existe, 1)
        }
        calcularTotal()
    }
}

const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(item => item.id !== id)    
}

const vaciarCarrito = () => {
    carrito.value = []
}





</script>

<template>
  <Header
  :carrito="carrito"
  :guitarra="guitarra"
  @incrementar-Cantidad = incrementarCantidad
  @decrementar-Cantidad = decrementarCantidad
  @eliminar-producto = eliminarProducto
  @vaciar-carrito = vaciarCarrito
  @agregar-carrito="agregarCarrito"
   />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
          <Guitarra 
            v-for="guitarra in guitarras" 
            v-bind:guitarra="guitarra"
            @agregar-carrito="agregarCarrito"
        />
        </div>
    </main>
    <Footer />
</template>


