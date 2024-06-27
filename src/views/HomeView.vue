<template>
  <main class="container text-white ">
    <div class="pt-4 mb-8 relative">
      <input @input="getSearchResult" v-model="searchQuery"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
        placeholder="Search for city or state" />
      <ul v-if="mapboxSearchResult"
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <p v-if="searchError">Somethignwent wrong</p>
        <p v-if="!searchError && mapboxSearchResult.length === 0">No results match your query, please try something
          diffrente</p>
        <template v-else>
          <li v-for="searchResult in mapboxSearchResult" :key="searchResult.id" class="py-2 cursor-pointer"
            @click="previewCity(searchResult)">{{
              searchResult.place_name }}</li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
        <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';
import CityList from '@/components/CityList.vue';
import CityCardSkeleton from '@/components/CityCardSkeleton.vue';

const router = useRouter();

const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";

const searchQuery = ref(null);
const queryTimeout = ref(null);
const mapboxSearchResult = ref(null);
const searchError = ref(null);

const getSearchResult = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place&units=metric`)
        mapboxSearchResult.value = result.data.features;
        return;
      } catch (error) {
        searchError.value = true
      }
    };
    mapboxSearchResult.value = null
  }, 300)
}

const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(',');

  router.push({
    name: "cityView",
    params: { state: state.replaceAll(' ', ""), city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    }
  })

}

</script>
