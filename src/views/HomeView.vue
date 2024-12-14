<template>
  <div class="home">
    <h1>Frontend Challenge - Weather App</h1>
  </div>
  <BuscadorCiudades @mostrar-proximos-5-dias="manejarProximos5Dias"/>
  <BuscadorHistorico v-if="ciudadSeleccionada" :ciudad="ciudadSeleccionada"/>
</template>

<script setup>
import BuscadorCiudades from '@/components/BuscadorCiudades.vue';
import BuscadorHistorico from '@/components/BuscadorHistorico.vue';
import {ref } from 'vue';

const ciudadSeleccionada = ref();

const manejarProximos5Dias = (ciudad) => {
  ciudadSeleccionada.value = ciudad;
};

</script>
