<template>
  <div>
    <h1>天気予報</h1>
    <div v-if="weatherData">
      <div v-for="(day, index) in weatherData.daily.time" :key="index">
        日付: {{ day.toLocaleDateString() }}
        <br>天気: <img :src="getWeatherImage(weatherData.daily.weatherCode[index])" alt="Weather icon">
        {{ getWeatherImage(weatherData.daily.weatherCode[index]) }}
        <br>最高気温: {{ weatherData.daily.temperature2mMax[index] }}°C
        <br>最低気温: {{ weatherData.daily.temperature2mMin[index] }}°C
        <br>降水確率: {{ weatherData.daily.precipitationProbabilityMax[index] }}%
        <hr>
      </div>
    </div>
    <div v-else>
      天気データの取得中...
    </div>
    <img :src="`assets/icons/${sunny}.png`" />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

const sunny = "sunny.png"

const weatherData = ref(null);

const params = {
  "latitude": 35.6895,   // 東京の緯度
  "longitude": 139.6917, // 東京の経度
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

const getWeatherImage = (code: string) => {
  const weatherCodeToImageUrl = {
    '0': 'assets/icons/sunny.png',
    '1': 'assets/icons/sunny_cloud.png',
    '2': 'assets/icons/sunny_rain.png',
    '3': 'assets/icons/sunny_snow.png',
    '45': 'assets/icons/rain_cloud.png',
    '48': 'assets/icons/rain_cloud.png',
    '51': 'assets/icons/rain_cloud.png',
    '53': 'assets/icons/rain_cloud.png',
    '55': 'assets/icons/rain_cloud.png',
    '56': 'assets/icons/rain_cloud.png',
    '57': 'assets/icons/rain_cloud.png',
    '61': 'assets/icons/cloud_rain.png',
    '63': 'assets/icons/cloud_rain.png',
    '65': 'assets/icons/cloud_rain.png',
    '66': 'assets/icons/cloud_rain.png',
    '67': 'assets/icons/cloud_rain.png',
    '71': 'assets/icons/cloud_snow.png',
    '73': 'assets/icons/cloud_snow.png',
    '75': 'assets/icons/cloud_snow.png',
    '77': 'assets/icons/cloud_snow.png',
    '80': 'assets/icons/rain.png',
    '81': 'assets/icons/rain.png',
    '82': 'assets/icons/rain.png',
    '85': 'assets/icons/snow_cloud.png',
    '86': 'assets/icons/snow_cloud.png',
    '95': 'assets/icons/rain.png',
    '96': 'assets/icons/rain_snow.png',
    '99': 'assets/icons/rain_snow.png'
  };

  return new URL(`${weatherCodeToImageUrl[code]}`, import.meta.url).href ;
  
};

onMounted(fetchData);
</script>
