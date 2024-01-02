<template>
  <div>
    <h1>天気予報</h1>
    <div v-if="weatherData">
      <div v-for="(day, index) in weatherData.daily.time" :key="index">
        日付: {{ day.toLocaleDateString() }}
        <br>天気: {{ getWeatherName(weatherData.daily.weatherCode[index]) }}
        <br>最高気温: {{ weatherData.daily.temperature2mMax[index] }}°C
        <br>最低気温: {{ weatherData.daily.temperature2mMin[index] }}°C
        <br>降水確率: {{ weatherData.daily.precipitationProbabilityMax[index] }}%
        <hr>
      </div>
    </div>
    <div v-else>
      天気データの取得中...
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

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

const getWeatherName = (code: string) => {
  const weatherCodeToName = {
    '0': '晴れ',
    '1': '晴れ時々曇り',
    '2': '晴れ一時雨',
    '3': '晴れ時々雪',
    '45': '霧',
    '48': '霧凍',
    '51': '霧雨',
    '53': '霧雨',
    '55': '霧雨',
    '56': '霧凍雨',
    '57': '霧凍雨',
    '61': '一時雨',
    '63': '一時雨',
    '65': '一時雨',
    '66': '一時凍雨',
    '67': '一時凍雨',
    '71': '一時雪',
    '73': '一時雪',
    '75': '一時雪',
    '77': 'みぞれ',
    '80': 'にわか雨',
    '81': 'にわか雨',
    '82': 'にわか雨',
    '85': 'にわか雪',
    '86': 'にわか雪',
    '95': '雷雨',
    '96': '雷雨と雹',
    '99': '雷雨と雹'
  };

  return weatherCodeToName[code] || '不明';
};

onMounted(fetchData);
</script>
