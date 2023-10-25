<template>
  <div class="bg-gray-100">
    <div v-if="loading" class="bg-blue-200 p-4 rounded-lg">
      <span class="text-xl font-semibold text-blue-600">Завантаження...</span>
    </div>
    <div v-else>
      <div v-if="error" class="bg-red-200 p-4 rounded-lg">
        <span class="text-xl font-semibold text-red-600">{{ error }}</span>
      </div>
      <div v-else>
        <div class="bg-white rounded-lg shadow-lg p-4">
          <div class="mb-4 text-center">
            <h1 class="text-2xl font-semibold">
              {{ weather.name }}, {{ weather.sys.country }}
            </h1>
            <p class="text-gray-600">
              {{ weather.coord.lat }}°, {{ weather.coord.lon }}°
            </p>
          </div>
          <table
            class="table-auto w-full border-collapse border border-gray-300"
          >
            <tr>
              <td class="border px-4 py-2">Вологість</td>
              <td class="border px-4 py-2">{{ weather.main.humidity }}</td>
              <td class="border px-4 py-2 empty"></td>
            </tr>
            <tr>
              <td class="border px-4 py-2">Темп/Сер.</td>
              <td class="border px-4 py-2">
                {{ convertedTemperature(weather.main.temp) }}°C
              </td>
              <td class="border px-4 py-2">{{ weather.main.temp }} K</td>
            </tr>
            <tr>
              <td class="border px-4 py-2">Основне</td>
              <td class="border px-4 py-2">{{ weather.weather[0].main }}</td>
              <td class="border px-4 py-2 empty"></td>
            </tr>
            <tr>
              <td class="border px-4 py-2">Тиск</td>
              <td class="border px-4 py-2">{{ weather.main.pressure }} гПа</td>
              <td class="border px-4 py-2 empty"></td>
            </tr>
            <tr>
              <td class="border px-4 py-2">Опис</td>
              <td class="border px-4 py-2">
                {{ weather.weather[0].description }}
              </td>
              <td class="border px-4 py-2 empty"></td>
            </tr>
            <tr>
              <td class="border px-4 py-2">Вітер</td>
              <td class="border px-4 py-2">{{ weather.wind.speed }} м/с</td>
              <td class="border px-4 py-2 empty"></td>
            </tr>
            <tr>
              <td class="border px-4 py-2">Напрямок</td>
              <td class="border px-4 py-2">{{ weather.wind.deg }}°</td>
              <td class="border px-4 py-2 empty"></td>
            </tr>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted, toRefs } from "vue";

const props = defineProps({
  city: String,
});
const { city } = toRefs(props);
const loading = ref(true);
const error = ref("");
const weather = ref({});
const apiKey = "7914d5a440960cfd5df3bd0388a7ad0f";

const convertedTemperature = (temp) => {
  return Math.round(temp - 273.15);
};

onMounted(async () => {
  await axios
    .get(
      `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&appid=${apiKey}`
    )
    .then(({ data }) => {
      weather.value = data;
    })
    .catch(() => {
      error.value = "Інформація про погоду не знайдена для цього міста.";
    })
    .finally(() => {
      loading.value = false;
    });
});
</script>
