<template>
  <div>
    <h1>天気予報</h1>
    <div v-if="weatherData">
      <div v-for="(day, index) in weatherData.daily.time" :key="index">
        日付: {{ day.toLocaleDateString() }}
        <br>天気コード: {{ weatherData.daily.weatherCode[index] }}
        <br>最高気温: {{ weatherData.daily.temperature2mMax[index] }}°C
        <br>最低気温: {{ weatherData.daily.temperature2mMin[index] }}°C
        <br>降水確率: {{ weatherData.daily.precipitationProbabilityMax[index] }}%
        <hr>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

const weatherData = ref(null);

const params = {
  "latitude": 35.6895,
  "longitude": 139.6917,
  "daily": ["weather_code", "temperature_2m_max", "temperature_2m_min", "precipitation_probability_max"],
  "timezone": "Asia/Tokyo"
};

const fetchData = async () => {
  const url = new URL("https://api.open-meteo.com/v1/forecast");
  Object.keys(params).forEach(key => url.searchParams.append(key, params[key]));
  try {
    const response = await fetch(url.toString());
    if (!response.ok) {
      throw new Error('データの取得に失敗しました。');
    }
    const data = await response.json();
    weatherData.value = {
      daily: {
        time: data.daily.time.map((t: string) => new Date(t)),
        weatherCode: data.daily.weather_code,
        temperature2mMax: data.daily.temperature_2m_max,
        temperature2mMin: data.daily.temperature_2m_min,
        precipitationProbabilityMax: data.daily.precipitation_probability_max,
      },
    };
  } catch (error) {
    console.error(error);
  }
};

onMounted(fetchData);
</script>