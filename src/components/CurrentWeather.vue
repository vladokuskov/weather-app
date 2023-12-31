<script setup lang="ts">
import { useLocationStore } from '@/stores/useLocationStore';
import { Languages } from '@/types/global/Languages.types';
import { convertTemperature } from '@/utils/convertTemperature';
import { storeToRefs } from 'pinia';
import { onMounted, ref, watch } from 'vue';
import { getWeatherIconName } from '../utils/getWeatherIconName';

const locationStore = useLocationStore()
const { geoPoint } = storeToRefs(locationStore)

const isLoading = ref<Boolean>(true)
const weatherData = ref<any>(null);

onMounted(() => {
    if (geoPoint.value) {
        fetchCurrentWeather()
    }
})

watch(geoPoint, () => {
    if (geoPoint.value) {
        fetchCurrentWeather()
    }
});


const fetchCurrentWeather = async () => {
    if (geoPoint.value) {
        const apiKey = import.meta.env.VITE_API_KEY;
        const latitude = geoPoint.value.lat;
        const longitude = geoPoint.value.lon;
        const apiUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=${apiKey}`;

        try {
            const response = await fetch(apiUrl);
            if (!response.ok) {
                throw new Error('Network response was not ok.');
            }

            const data = await response.json();
            weatherData.value = data;
            isLoading.value = false;
        } catch (error) {
            isLoading.value = false;
            throw new Error('Can not load weather right now.');
        }
    }

};


</script>

<template>
    <section>
        <div v-if="isLoading" class="skeleton" />
        <div v-else class="weather-card-wrapper">
            <div class="weather-card-image-wrapper">
                <img alt="Weather icon" class="weather-image" width="100" height="100"
                    :src="`../assets/weather/${getWeatherIconName(weatherData.weather[0].id)}.svg`" />
            </div>
            <div class="weather-card-info" v-if="weatherData">
                <p class="info-temp">{{ convertTemperature(weatherData.main.temp) }}°C</p>
                <p>{{ weatherData.name }}, {{ weatherData.weather[0].description }}</p>
                <p>{{ new Date().toLocaleString('en-GB', { hour: 'numeric', minute: 'numeric' }) }}</p>
            </div>
            <p v-else class="not-found">No weather data available.</p>
        </div>
    </section>
</template>

<style scoped>
section {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 6rem;
}

.weather-card-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    gap: 1rem;
    max-width: 15rem;
    min-height: 8rem;
    min-width: 13rem;
    width: 100%;
    height: 20rem;
}

.weather-card-info {
    font-display: optional;
    font-family: Inter, sans-serif;
    font-weight: 500;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: #797979;

}

.not-found {
    font: optional;
    font-family: Roboto, sans-serif;
    color: rgba(67, 67, 67, 1);
    font-weight: 400;
}

.info-temp {
    font-size: 3.25rem;
    color: #434343;
    letter-spacing: -5px;
}

.weather-card-image-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    max-width: 11rem;
    max-height: 11rem;
    min-height: 8rem;
    min-width: 8rem;
    width: 100%;
    height: 100%;
}

.weather-image {
    height: auto;
    width: 100%;
}

.skeleton {
    max-width: 15rem;
    min-height: 8rem;
    min-width: 13rem;
    width: 100%;
    height: 20rem;
    background-color: #ececec6b;
    border-radius: 8px;
    animation: pulse 1.3s infinite;
}
</style>