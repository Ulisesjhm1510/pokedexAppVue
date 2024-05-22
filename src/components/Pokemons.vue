<script setup>
import { ref } from 'vue';
import { pokeapi } from '@/api/pokeapi';

// Reactive state variables
const pokemonData = ref({});
const pokemonID = ref('');

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
</script>
<template>
    <div>
        <header class="header">
            <label for="pokemon-search">
                <h1>Escribe el Pokemon que quieres buscar!</h1>
                <input id="pokemon-search" v-model="pokemonID" type="text" placeholder="Enter Pokemon ID or Name">
                <button @click="searchPokemon">Buscar</button>
            </label>
        </header>
        <main class="main">
            <section v-if="pokemonData.name" class="pokemonCard">
                <div class="nameImage">
                    <h1 class="pokemonName">{{ pokemonData.name }}</h1>
                    <img :src="pokemonData.sprites.front_default" :alt="pokemonData.name">
                </div>
                <ul class="type">
                    <h2>Clase:</h2>
                    <li v-for="type in pokemonData.types" :key="type.type.name">
                        <span>{{ type.type.name }}</span>
                    </li>
                </ul>
                <ul class="stats">
                    <h2>Stats:</h2>
                    <li v-for="stat in pokemonData.stats" :key="stat.stat.name">
                        <span>{{ stat.stat.name }}: {{ stat.base_stat }}</span>
                    </li>
                </ul>
            </section>
        </main>
    </div>
</template>

<style scoped>
/* Add some basic styling */
.header {
    text-align: center;
    margin-bottom: 20px;
}

.header label {
    display: block;
}

.header input {
    margin-right: 10px;
}

.pokemonCard {
    display: flex;
    flex-direction: column;
    align-items: center;
}

.nameImage img {
    width: 150px;
    height: 150px;
}

.type,
.stats {
    list-style: none;
    padding: 0;
}

.type li,
.stats li {
    margin-bottom: 5px;
}
</style>