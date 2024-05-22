<template>
    <div>
      <header class="text-center mb-5">
        <label for="pokemon-search" class="block">
          <h1>Escribe el Pokemon que quieres buscar!</h1>
          <input
            id="pokemon-search"
            v-model="pokemonID"
            type="text"
            placeholder="Enter Pokemon ID or Name"
            class="mr-2 p-1 border rounded"
          />
          <button
            @click="searchPokemon"
            class="bg-blue-500 text-white py-1 px-3 rounded hover:bg-blue-700"
          >
            Buscar
          </button>
        </label>
      </header>
      <main class="flex justify-center flex-wrap">
        <!-- Display Pokemon Cards -->
        <template v-for="(pokemon, index) in paginatedPokemonList" :key="index">
          <div class="pokemon-card bg-white shadow-lg rounded-lg p-5 w-1/5 max-w-md m-3 flex">
            <div class="w-1/3 flex flex-col items-center justify-center">
              <h1 class="text-2xl font-bold capitalize">{{ pokemon.name }}</h1>
              <img
                :src="pokemon.sprites.front_default"
                :alt="pokemon.name"
                class="w-36 h-36 mt-4"
              />
            </div>
            <div class="w-2/3 pl-5">
              <div class="mb-4">
                <h2 class="text-xl font-semibold">Clase:</h2>
                <ul class="list-none p-0">
                  <li
                    v-for="type in pokemon.types"
                    :key="type.type.name"
                    class="mb-1 capitalize"
                  >
                    <span>{{ type.type.name }}</span>
                  </li>
                </ul>
              </div>
              <div>
                <h2 class="text-xl font-semibold">Stats:</h2>
                <ul class="list-none p-0">
                  <li
                    v-for="stat in pokemon.stats"
                    :key="stat.stat.name"
                    class="mb-1 capitalize"
                  >
                    <span>{{ stat.stat.name }}: {{ stat.base_stat }}</span>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </template>
      </main>
      <!-- Pagination Controls -->
      <div class="mt-5 flex justify-center">
        <button
          v-for="pageNumber in totalPages"
          :key="pageNumber"
          @click="currentPage = pageNumber"
          class="bg-blue-500 text-white py-1 px-3 rounded hover:bg-blue-700 mr-2"
        >
          {{ pageNumber }}
        </button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, computed } from 'vue';
  import { pokeapi } from '@/api/pokeapi';
  
  // Reactive state variables
  const pokemonData = ref({});
  const pokemonID = ref('');
  const pokemonList = ref([]);
  
  // Methods
  const searchPokemon = async () => {
    try {
      const response = await fetch(`${pokeapi}/${pokemonID.value.toLowerCase()}`);
      if (response.ok) {
        pokemonData.value = await response.json();
      } else {
        console.error("Pokemon not found");
        pokemonData.value = {};  // Clear the data if not found
      }
    } catch (error) {
      console.error("Error fetching Pokemon data:", error);
      pokemonData.value = {};  // Clear the data on error
    }
  };
  
  const fetchAllPokemon = async () => {
    try {
      let allPokemon = [];
      let nextUrl = `${pokeapi}`;
      
      // Fetch all pages of Pokemon data
      while (nextUrl) {
        const response = await fetch(nextUrl);
        if (response.ok) {
          const data = await response.json();
          allPokemon = [...allPokemon, ...data.results];
          nextUrl = data.next; // Update nextUrl to fetch the next page
        } else {
          console.error("Failed to fetch Pokemon data");
          break;
        }
      }
      
      pokemonList.value = allPokemon;
    } catch (error) {
      console.error("Error fetching Pokemon list:", error);
    }
  };
  
  onMounted(() => {
    fetchAllPokemon();
  });
  
  // Pagination Logic
  const currentPage = ref(1);
  const itemsPerPage = 20;
  
  const paginatedPokemonList = computed(() => {
    const start = (currentPage.value - 1) * itemsPerPage;
    const end = start + itemsPerPage;
    return pokemonList.value.slice(start, end);
  });
  
  const totalPages = computed(() => Math.ceil(pokemonList.value.length / itemsPerPage));
  </script>
  