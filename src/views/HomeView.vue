<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        v-model="searchQuery"
        @input="getSearchResults"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
        type="text"
        placeholder="Search for a city or state"
      />
      <ul
        v-if="openWeatherSearchResults"
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
      >
        <li
          v-for="searchResult in openWeatherSearchResults"
          :key="searchResult.id"
          class="py-2 cursor:pointer"
        >
          {{ searchResult.name }}
        </li>
      </ul>
    </div>
  </main>
</template>
<script setup>
import { ref } from "vue";
import axios from "axios";

const searchQuery = ref("");
const queryTimeout = ref(null);
const openWeatherSearchResults = ref(null);

const openWeatherKey = "ca194fba23b86496895c7c28c0543ddf";

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);

  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      const result = await axios.get(
        `http://api.openweathermap.org/geo/1.0/direct?q=${searchQuery.value}&limit=5&appid=${openWeatherKey}`
      );

      openWeatherSearchResults.value = result.data;
      console.log("sankdkasd", openWeatherSearchResults.value);
      return;
    }
    openWeatherSearchResults.value = null;
  }, 300);
};
</script>
