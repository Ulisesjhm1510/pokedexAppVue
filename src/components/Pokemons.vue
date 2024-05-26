<template>
  <div>
    <header class="text-center mb-5">
      <h1>Which Pokemon do you want to search?</h1>
      <label for="pokemon-search" class="block">
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
          Search
        </button>
      </label>
    </header>
    <main class="flex justify-center flex-wrap">
      <div v-if="pokemonData.name" class="text-center">
        <h1>{{ pokemonData.name }}</h1>
        <img :src="pokemonData.sprites?.front_default" :alt="pokemonData.name" />
      </div>
    </main>
  </div>
</template>
  
  <script setup>
  import { ref } from 'vue';
  import { pokeapi } from '@/api/pokeapi';
  
  // Reactive state variables
  const pokemonData = ref({});
  const pokemonID = ref('');
  
  // Methods
  const searchPokemon = async () => {
    try {
      const response = await fetch(`${pokeapi}/${pokemonID.value.toLowerCase()}/`);
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
  </script>
  