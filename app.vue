<template>
  <div>
    <h1>天気予報</h1>
    <div v-if="weatherData">
      <!-- 天気情報の表示 -->
      <p>{{ weatherData.title }}</p>
      <p v-for="forecast in weatherData.forecasts" :key="forecast.dateLabel">
        {{ forecast.dateLabel }}: {{ forecast.telop }}
      </p>
    </div>
  </div>
</template>

<script setup>
const { data: weatherData, pending, error } = await useFetch(
  'https://weather.tsukumijima.net/api/forecast/city/130010'
)
</script>