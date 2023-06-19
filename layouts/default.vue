<template>
  <header>
    <div class="wrapper">
      <div class="header-wrapper">
        <div class="trademark">
          <div class="placeholder-icon">
            <img src="../public/favicon.ico" alt="" srcset="">
          </div>
          <nuxt-link to="/">
            <h1>WeatherWiz</h1>
          </nuxt-link>
        </div>
        <div class="result-wrapper">
          <input type="text" placeholder="Search city..." class="search-city" v-model="searchQuery"
            @input="getSearchResults">
          <ul class="list-class" v-if="searchResults && showResults">
            <p v-if="searchError" class="error-text">Something went wrong.. </p>
            <p v-if="!searchError && !searchResults[1]" class="error-text"> No match. Try a different query.</p>
            <template v-else>
              <li v-for="searchResult in searchResults" :key="searchResult.lat" class="list-item">
                <NuxtLink :to="{
                  name: 'CityView',
                  query: { name: searchResult.name, lat: searchResult.lat, lon: searchResult.lon }
                }" @click="hideResults()">
                  {{ searchResult.name }}, {{ searchResult.state ? searchResult.state + "," : "" }} {{
                    searchResult.country }}
                </NuxtLink>
              </li>
            </template>
          </ul>
        </div>
      </div>
    </div>
  </header>
  <!-- Content -->
  <slot />
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";

const router = useRouter()
const owmAPIKey = "7a02120c6d63e7c7e58d4cefe56ef1f8"
const searchQuery = ref("");
const queryTimeout = ref(null);
const searchResults = ref(null);
const searchError = ref(null);
let showResults = true;

const getSearchResults = () => {
  showResults = true;
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(` http://api.openweathermap.org/geo/1.0/direct?q=${searchQuery.value}&limit=5&appid=${owmAPIKey}`);
        searchResults.value = result.data;
      } catch {
        searchError.value = true;
      }
      console.log(searchResults.value);
      return;
    }
    searchResults.value = null;
  }, 1000);
};

const hideResults = () => {
  showResults = false;
}
</script>

<style> * {
   box-sizing: border-box;
   padding: 0;
   margin: 0;
 }

 .router-link-exact-active.router-link-active {
   text-decoration-line: none;
   color: white;
 }

 .router-link-active:hover {
   opacity: 0.8;
 }

 a {
   text-decoration-line: none;
   color: white;
 }

 .placeholder-icon {
   margin-top: 8px;
 }

 .error-text {
   text-align: center;
 }

 body {
   background-color: #015b7f;
   color: #f7f6f7;
   font-size: 1.6rem;
   padding: 30px;
 }

 .wrapper {
   display: flex;
   justify-content: center;
 }

 .header-wrapper {
   display: flex;
   flex-direction: column;
   gap: 40px;
   padding-bottom: 10px;
 }

 .trademark {
   display: flex;
   justify-content: center;
   gap: 20px;
   font-size: 1.2rem;
 }

 .result-wrapper {
   width: 500px;
 }

 .search-city {
   width: 100%;
   height: 50px;
   padding: 20px;
   background-color: #004665;
   border: none;
   border-radius: 100px;
   font-size: 1.6rem;
   color: #f7f6f7;
 }

 .search-city:focus {
   outline: 0;
 }

 .list-class {
   position: absolute;
   background-color: #004665;
   color: white;
   width: 500px;
   padding: 8px;
   list-style: none;
   border-radius: 20px;
   margin-top: 10px;
 }

 .list-item {
   padding: 8px 4px;
   cursor: pointer;
 }

 .list-item:hover {
   opacity: 0.8;
 }
</style>