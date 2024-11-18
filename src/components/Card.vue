<template>
   <div class="pokemon-card">
      <div class="card border-0 text-center">

         <!-- Imagen difuminada con blur y grayscale -->
         <img
            :src="pokemonImageUrl"
            :class="{ grayscaleBlur: !discovered,'dance-img': dance }"
            :style="{ maxWidth: discovered ? '120px' : '80px' }"
            class="img-fluid mb-1"
         /> <!-- Aquí añadí la clase dance-img para la animación -->

         <!-- Input y botón sólo visibles si no está descubierto -->
         <div v-if="!discovered">
            <input type="text" v-model="guessedName" class="text-center mb-2 input-group input-group-sm border border-seconday rounded" placeholder="¿Quién es?" />
            <div class="d-flex justify-content-center">
               <button class="btn btn-sm btn-outline-warning" type="button" @click="checkName" > Descubrir </button>
            </div>
         </div>

         <!-- Mensaje al adivinar el Pokémon -->
         <p v-if="discovered">{{ resultMessage }}</p>

         <!-- Modal para mostrar mensaje de error -->
         <div v-if="showModal" class="modal-backdrop" @click="closeModal">
            <div class="modal-content">
               <h3 class="p-3">{{ errorMessage }}</h3>
               <p class="fs-5">Nombre incorrecto. Sigue intentando... ¡Tu puedes!</p>
               <button class="btn btn-outline-danger mt-2" @click="closeModal">Cerrar</button>
            </div>
         </div>
      </div>
   </div>
</template>
 
<script setup>
   import { ref, computed } from 'vue';

   // Recibimos los datos del Pokémon desde el componente padre
   const props = defineProps({
      pokemon: Object,
      dance: Boolean   // Propiedad para controlar si la imagen baila
   });

   // Evento pokemon-discovered que agrega interacción al emitirse cuando se adivina el pokemon
   const emit = defineEmits(['pokemon-discovered']);

   // Estado local para manejar la adivinanza
   const guessedName = ref('');
   const discovered = ref(false);
   const resultMessage = ref('');
   const showModal = ref(false);
   const errorMessage = ref('');

   // Propiedad computada para manejar la imagen del Pokémon
   const pokemonImageUrl = computed(() => {
      return props.pokemon.sprites.front_default;
   });
   
   // Función para comprobar si el nombre es correcto que se emite al padre
   const checkName = () => {
      if (guessedName.value.toLowerCase() === props.pokemon.name.toLowerCase()) {
         resultMessage.value = `¡Correcto! Es ${props.pokemon.name}`;
         discovered.value = true;
         emit('pokemon-discovered'); // Emitimos evento para incrementar el contador
      } else {
         errorMessage.value = `⛔ ¡No es el pokemon oculto! 🫤`;
         showModal.value = true;
      }
   };
   
   // Función para cerrar el modal y limpiar el input
   const closeModal = () => {
      showModal.value = false;
      guessedName.value = '';
   };
</script>
 
<style scoped>
   .pokemon-card {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 5px;
      border-radius: 12px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
   }
   .card {
      display: flex;
      align-items: center;
      justify-content: center;
   }
   
   .card img {
      max-width: 80px;
   }
   
   .grayscaleBlur {
      filter: grayscale(100%) blur(4px);
      transition: filter 0.3s eae;
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