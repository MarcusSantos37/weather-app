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
        <p v-if="searchError">Sorry, something went wrong, please try again.</p>
        <p v-if="!serverError && openWeatherSearchResults.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            v-for="searchResult in openWeatherSearchResults"
            :key="searchResult.id"
            class="py-2 cursor:pointer"
            @click="previewCity(searchResult)"
          >
            {{
              `${searchResult.name}${
                searchResult.state ? `, ${searchResult.state}` : ``
              } - ${searchResult.country}`
            }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

const router = useRouter();

const previewCity = (searchResult) => {
  router.push({
    name: "cityView",
    params: {
      state: searchResult.state.replaceAll(" ", ""),
      city: searchResult.name.replaceAll(" ", ""),
    },
    query: {
      lat: searchResult.lat,
      lng: searchResult.lon,
      preview: true,
    },
  });
};

const searchQuery = ref("");
const queryTimeout = ref(null);
const openWeatherSearchResults = ref(null);
const searchError = ref(null);

const openWeatherKey = process.env.OPEN_WEATHER_KEY;

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);

  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `http://api.openweathermap.org/geo/1.0/direct?q=${searchQuery.value}&limit=5&appid=${openWeatherKey}`
        );

        openWeatherSearchResults.value = result.data;

        console.log("sladlasd", result.data);
      } catch {
        searchError.value = true;
      }
      return;
    }
    openWeatherSearchResults.value = null;
  }, 300);
};
</script>
