<template>
  <div class="weather-wrapper">
    <div class="city-current">
      <h2>{{ route.query.name }}</h2>
      <h1 id="temp-info">{{ weatherData.data.current_weather.temperature }} ° C</h1>
      <div>
        <img :src="setWeatherIcon(weatherData.data.current_weather.weathercode)" alt="Current weather icon"
          id="current-img">
        <p class="weather-name">{{ weatherName }}</p>
      </div>
      <p> Feels like {{ weatherData.data.hourly.apparent_temperature[index] }} ° C</p>
      <div class="detailed-info">
        <p>Windspeed: {{ weatherData.data.current_weather.windspeed }} km/s</p>
        <p>Visibility: {{ (weatherData.data.hourly.visibility[index] / 1000) }} km</p>
      </div>
    </div>
    <div class="weather-section">
      <h1>Next 10 hours</h1>
      <div class="hourly-weather">
        <ul class="weather-list">
          <li v-for="hourIndex in 10">
            <div class="weather-box">
              <img :src="setWeatherIcon(weatherData.data.hourly.weathercode[index + hourIndex - 1])" alt="">
              <p>{{ weatherData.data.hourly.temperature_2m[index + hourIndex - 1] }}°</p>
              <p>{{ weatherData.data.hourly.time[index + hourIndex - 1].substring(11, 16) }}</p>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <div class="weather-section">
      <h1>Daily</h1>
      <div class="daily-weather">
        <ul class="weather-list">
          <li v-for="index in 7">
            <div class="weather-box">
              <p>{{ weatherData.data.daily.time[index - 1] }}</p> <br>
              <img :src="setWeatherIcon(weatherData.data.daily.weathercode[index - 1])" alt="">
              <div class="min-max-temp">
                <p>{{ weatherData.data.daily.temperature_2m_max[index - 1] }}°</p>
                <p>{{ weatherData.data.daily.temperature_2m_min[index - 1] }}°</p>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <div class="weather-section">
      <h1>Details</h1>
      <div class="daily-details">
        <div class="sun-info">
          <h2 class="daily-subtitle">Sunrise</h2>
          {{ weatherData.data.daily.sunrise[0].substring(11, 16) }}<br>
          <h2 class="daily-subtitle">Sunset</h2>
          {{ weatherData.data.daily.sunset[0].substring(11, 16) }}
        </div>
        <div class="uv-info">
          <h2 class="daily-subtitle">Precipitation Probability</h2>
          {{ weatherData.data.daily.precipitation_probability_max[0] }}%
          <h2 class="daily-subtitle">UV Index</h2>
          {{ weatherData.data.daily.uv_index_max[0] }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute } from 'vue-router';
import * as images from '@/assets/images';

const route = useRoute();
const getWeatherData = async () => {
  //Delay for anti flickering
  await new Promise((res) => setTimeout(res, 1000));
  try {
    const weatherData = await axios.get(`https://api.open-meteo.com/v1/forecast?latitude=${route.query.lat}&longitude=${route.query.lon}&hourly=temperature_2m,weathercode,apparent_temperature,visibility&daily=weathercode,temperature_2m_max,temperature_2m_min,sunrise,sunset,uv_index_max,precipitation_probability_max&current_weather=true&timezone=auto`);
    return weatherData;
  } catch (err) {
    console.log(err);
  }
};

let weatherData = await getWeatherData();
let index = weatherData.data.hourly.time.indexOf(weatherData.data.current_weather.time);
let weatherName = " ";

const setWeatherIcon = (weatherCode) => {
  let rainyWeatherCodes = [51, 53, 55, 56, 57, 61]
  let showerRainCodes = [63, 65, 66, 67, 80, 81, 82]
  let snowCodes = [71, 73, 75, 77, 85, 86]
  if (weatherCode == 0) {
    weatherName = "Sunny";
    return images.clearSky;
  }
  if (weatherCode == 1) {
    weatherName = "Few Clouds";
    return images.fewClouds
  }
  if (weatherCode == 2) {
    weatherName = "Scattered Clouds";
    return images.scatteredClouds
  }
  if (weatherCode == 3) {
    weatherName = "Cloudy";
    return images.overcast
  }
  if (weatherCode == 45 || weatherCode == 48) {
    weatherName = "Foggy"
    return images.fog
  }
  if (rainyWeatherCodes.includes(weatherCode)) {
    weatherName = "Light Rain"
    return images.rain
  }
  if (showerRainCodes.includes(weatherCode)) {
    weatherName = "Rain Shower"
    return images.showerRain
  }
  if (snowCodes.includes(weatherCode)) {
    weatherName = "Snowy"
    return images.snow
  }
  if (weatherCode == 95 || weatherCode == 96 || weatherCode == 99) {
    weatherName = "Thunder Storm"
    return images.thunderstorm
  }
}

const updateData = async () => {
  weatherData = await getWeatherData();
};

watch(() => route.query, async () => {
  await updateData();
},
);
</script>

<style>
a:hover {
  opacity: 0.8;
}

.weather-name {
  margin-top: -25px;
  margin-bottom: 25px;
}

.weather-wrapper {
  margin: 10vh auto;
  text-align: center;
}

.weather-section {
  text-align: left;
  margin: 100px auto;
  max-width: 1600px;
}

#temp-info {
  margin: 10px;
}

#current-img {
  width: 150px;
}

.detailed-info {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-top: 10px;
}

.weather-list {
  list-style: none;
  margin-top: 30px;
  display: flex;
  justify-content: space-between;
}

.weather-box {
  display: flex;
  flex-direction: column;
  text-align: center;
  padding: 20px;
}

.weather-box:hover {
  opacity: 0.8;
  background-color: #004665;
}

.daily-details {
  display: flex;
  gap: 50px;
}

.min-max-temp {
  display: flex;
  justify-content: space-around;

}

.sun-info {
  border-top: 1px solid rgb(168, 214, 255);
  width: 40%;
  margin-top: 30px;
}

.daily-subtitle {
  margin-top: 20px;
}

.uv-info {
  border-top: 1px solid rgb(168, 214, 255);
  width: 40%;
  margin-top: 30px;
}
</style>