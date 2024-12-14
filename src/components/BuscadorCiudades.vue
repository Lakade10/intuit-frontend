<template>

<h3>Buscador de ciudades</h3>
<div cass="search-container">
    <InputText type="text" v-model="busqueda" @input="buscarCiudades" placeholder="Ingresa una ciudad..." />
    <Card  v-if="ciudad">
        <template #title>
          {{ ciudad.name }}, {{ ciudad.sys.country }}
        </template>
        <template #content>
            <p><strong>Temperatura:</strong> {{kelvinACelcius(ciudad.main.temp) }} °C</p>
            <p><strong>Máxima:</strong> {{ kelvinACelcius(ciudad.main.temp_max) }} °C</p>
            <p><strong>Mínima:</strong> {{ kelvinACelcius(ciudad.main.temp_min) }} °C</p>
            <p><strong>Humedad:</strong> {{ ciudad.main.humidity }}%</p>
            <p><strong>Clima:</strong> {{ ciudad.weather[0].description }}</p>
        </template>
        <template #footer>
            <div class="card-footer-container">
                <Button @click="$emit('mostrar-proximos-5-dias', ciudad.name)" class="p-button p-component">
                  Mostrar Próximos 5 Días
                </Button>
            </div>
        </template>
    </Card>
</div>

</template>
<script setup>
import { InputText } from 'primevue';
import { api } from '@/api'; 
import { ref } from 'vue';
import Card from 'primevue/card';
import Button from 'primevue/button';

const busqueda = ref('');

const ciudad = ref(null);

const buscarCiudades = async () => {
  if (busqueda.value.trim().length < 3) {
    ciudad.value = null;
    return;
  }
  try {
    const response = await api.get(`/weather?q=${busqueda.value}&appid=2ff897cf160efafab247252b1b4a56a9`);
    console.log(response);
    ciudad.value = response.data;
  } catch (error) {
    console.error("Error buscando ciudades:", error);
  }
};

const kelvinACelcius = (kelvin) => {
  return (kelvin - 273.15).toFixed(2);
};

</script>
<style scoped>
    .card-footer-container {
        display: flex;
        justify-content: center;
        gap: 20px;
    }
</style>