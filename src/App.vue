<script setup>
    import {ref, reactive, onMounted, watch} from 'vue';
    import {db} from './data/guitarras'
    import Guitarra from './components/guitarra.vue'
    import Header from './components/header.vue'
    import Footer from './components/footer.vue'

    const guitarras = ref([])
    const carrito = ref([])
    const guitarra = ref({})

    watch(carrito, ()=>{
        guardarLocalStorage()
    }, {
        deep: true
        
    })

    onMounted(() => {
        guitarras.value = db;
        guitarra.value = db[3];

        const carritoStorage = localStorage.getItem('carrito')
        if(carritoStorage){
            carrito.value = JSON.parse(carritoStorage)
        }
    })

    const guardarLocalStorage = () => {
        localStorage.setItem('carrito', JSON.stringify(carrito.value))
    }

    const agregarCarrito = (guitarra) => {
        const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
        if(existeCarrito >= 0 && carrito.value[existeCarrito].cantidad <5){
            carrito.value[existeCarrito].cantidad++
        }
        else if(existeCarrito === -1){
            guitarra.cantidad = 1
            carrito.value.push(guitarra)
        }
        else{
            return
        }
    }   
    const decrementarCantidad= (id)=>{
        const index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad <=1) {
            eliminarProducto(id)
        }
        else{
            carrito.value[index].cantidad--
            console.log(id)
        }
        
    }
    const incrementarCantidad= (id)=>{
        const index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad >=5) return
        carrito.value[index].cantidad++
        console.log(id)
    }

    const eliminarProducto = (id)=>{
        console.log(id)
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
    @incrementar-Cantidad="incrementarCantidad"
    @decrementar-Cantidad="decrementarCantidad"
    @agregar-Carrito="agregarCarrito"
    @eliminar-Producto="eliminarProducto"
    @vaciar-Carrito="vaciarCarrito"
  />

  <main class="container-xl mt-5">
      <h2 class="text-center">Nuestra Colección</h2>

      <div class="row mt-5">
        <Guitarra 
            v-for="guitarra in guitarras"
            v-bind:guitarra = "guitarra"
            @agregar-Carrito="agregarCarrito"
        />
      </div>
  </main>


  <Footer/>
</template>

<style scoped>

</style>
