<template>
   <div class="container text-center">
      <img :src="logoUrl" class="my-1" />
      <h2 class="mb-2">¿Quién es ese Pokémon?</h2>
      <h5 class="mb-5">Pokemones descubiertos: <span class="text-warning tw-bold fs-3 counter">{{ discoveredCount }}</span></h5>

      <!-- Renderización dinámica de Card.vue -->
      <div class="pokemon-grid">
         <Card
            v-for="(pokemon, index) in pokemons"
            :key="index"
            :pokemon="pokemon"
            @pokemon-discovered="incrementCounter"
            :dance="discoveredCount === 20"
         />  <!-- Pasamos la prop dance para aplicar la clase de la animación -->   
      </div>
      
      <!-- Modal de victoria -->
      <div v-if="showWinModal" class="modal-backdrop" @click="closeWinModal">
         <div class="modal-content">
            <p class="fs-6 uno">Has adivinado los 20 Pokémon 🙌🏼</p>
            <h1 class="pb-4 text-danger">🏆 ¡Felicidades! 🏆</h1>
            <p class="fs-5 dos">Ganaste esta partida ¿Quieres jugar nuevamente!</p>
            <button class="btn btn-success mt-2" @click="closeWinModal">Cerrar</button>
         </div>
      </div>
   </div>
</template>
 
<script setup>
   import { ref, onMounted, computed } from 'vue';
   import axios from 'axios';
   import Card from './components/Card.vue';
   import logoPok from './assets/img/logoPok.png';

   // Estado para almacenar los Pokemon
   const pokemons = ref([]);
   const discoveredCount = ref(0);
   const showWinModal = ref(false);
   
   // Función para obtener 20 Pokmons aleatorios
   const getPokemons = async () => {
      const randomPokemons = [];
      for (let i = 0; i < 20; i++) {
         const randomId = Math.floor(Math.random() * 100) + 1;
         const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${randomId}`);
         randomPokemons.push(response.data);
      }
      pokemons.value = randomPokemons;
   };
 
   // Computed para la URL del logo
   const logoUrl = computed(() => {
      return logoPok;
   });
   
   // Incrementa el contador cuando un Pokémon es adivinado
   const incrementCounter = () => {
      discoveredCount.value++;
      if (discoveredCount.value === 20) {
      showWinModal.value = true;
      }
   };
 
   // Cierra el modal de victoria
   const closeWinModal = () => {
      showWinModal.value = false;
      window.location.reload(true)   // Con true el navegador ignora la caché y solicita la página nuevamente desde el servidor
   };
   
   // Llamamos a la API cuando el componente se monta
   onMounted(() => {
      getPokemons();
   });
</script>
 
<style scoped>
   img {
      max-width: 150px;
      margin: 0 auto;
   }
   
   .pokemon-grid {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      grid-gap: 20px; 
      margin: 0 auto;
      max-width: 1200px;
   }
   
   @media (max-width: 900px) {
      .pokemon-grid {
      grid-template-columns: repeat(4, 1fr); 
      }
   }
   
   @media (max-width: 700px) {
      .pokemon-grid {
      grid-template-columns: repeat(3, 1fr); 
      }
   }
   @media (max-width: 500px) {
      .pokemon-grid {
      grid-template-columns: repeat(2, 1fr); 
      }
   }
   
   .modal-backdrop {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      background-color: rgba(0, 0, 0, 0.5);
      display: flex;
      justify-content: center;
      align-items: center;
   }
   
   .modal-content {
      background-color: white;
      padding: 20px;
      border-radius: 8px;
      text-align: center;
      width: 30%;
   }

   @media screen and (max-width: 700px) {
      .modal-content {
         width: 80%;
         padding: 15px;
      }
      .modal-content h1 {
         font-size: 2rem;
      }
      .uno {
         font-size: 0.9rem;
      }
      .dos {
         font-size: 0.9rem;
      }
      .btn {
         font-size: 0.9rem;
      }
   }

   .dance-img {
      animation: dance 2s infinite;
   }

   @keyframes dance {
      0% { transform: rotate(0deg) translateY(0); }
      25% { transform: rotate(-10deg) translateY(-10px); }
      50% { transform: rotate(10deg) translateY(10px); }
      75% { transform: rotate(-10deg) translateY(-10px); }
      100% { transform: rotate(0deg) translateY(0); }
   }
</style> 