<template>
<div>
    <h3>Pronostico a 5 días en {{ ciudad }}</h3>
    <div class="forecast-container">
        <Card v-for="dia in pronostico" :key="dia.fecha">
            <template #title>
                <h4>{{ dia.fecha }}</h4>
            </template>
            <template #content>
                <p><strong>Temperatura mínima:</strong> {{ kelvinACelcius(dia.temp_min) }} °C</p>
                <p><strong>Temperatura máxima:</strong> {{ kelvinACelcius(dia.temp_max) }} °C</p>
                <p><strong>Humedad:</strong> {{ dia.humedad }}%</p>
                <p><strong>Descripción:</strong> {{ dia.descripcion }}</p>
            </template>
        </Card>
    </div>
</div>
</template>

<script setup>
import { ref, watch, defineProps } from 'vue';
import Card from 'primevue/card';
import { api } from '@/api';

const props = defineProps({
    ciudad: String,
});

const pronostico = ref([]);

const kelvinACelcius = (kelvin) => {
    return (kelvin - 273.15).toFixed(2);
};  

const obtenerClimaProximos5Dias = async () => {
try {
    const response = await api.get(`/forecast?q=${props.ciudad}&appid=2ff897cf160efafab247252b1b4a56a9`);
    const datos = response.data.list;

    pronostico.value = [];
    datos.forEach((prediccion) => {
        const fecha = prediccion.dt_txt.split(' ')[0];

        // Evitar duplicados: Solo agregar la primera entrada para cada día
        if (!pronostico.value.some((p) => p.fecha === fecha)) {
            pronostico.value.push({
                fecha,
                temp_min: prediccion.main.temp_min,
                temp_max: prediccion.main.temp_max,
                humedad: prediccion.main.humidity,
                descripcion: prediccion.weather[0].description,
            });
        }
    });

    if (pronostico.value.length === 6){
        pronostico.value.pop();
    }

} catch (error) {
    console.error('Error obteniendo el clima:', error);
}
};

watch(
  () => props.ciudad,
  async (nuevaCiudad, antiguaCiudad) => {
    if (nuevaCiudad && nuevaCiudad !== antiguaCiudad) {
      pronostico.value = []; // Reinicia los datos antes de obtener nueva información
      await obtenerClimaProximos5Dias(); // Llama a la función asíncrona
    }
  },
  { immediate: true } // Activa el `watch` inmediatamente
);

</script>

<style scoped>
.forecast-container {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    justify-content: center;
}
</style>
