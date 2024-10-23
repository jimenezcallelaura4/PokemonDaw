<template>
  <div class="pokedex">
    <h1>Pokedex</h1>
    <div class="search-bar">
      <input v-model="searchQuery" placeholder="Nombre del pokemón" />
      <button @click="searchPokemon">Buscar</button>
    </div>
    <div class="pokemon-container">
      <div v-for="(pokemon, index) in filteredPokemons" :key="index" class="pokemon-card">
        <img :src="pokemon.image" :alt="pokemon.name" />
        <p>{{ pokemon.name }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      pokemons: [],
      searchQuery: "",
    };
  },
  computed: {
    filteredPokemons() {
      if (this.searchQuery) {
        return this.pokemons.filter((pokemon) =>
          pokemon.name.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      }
      return this.pokemons;
    }
  },
  mounted() {
    this.fetchPokemons();
  },
  methods: {
    async fetchPokemons() {
      try {
        const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=151');
        const pokemonData = response.data.results;

        const pokemonList = await Promise.all(
          pokemonData.map(async (pokemon) => {
            const pokeDetails = await axios.get(pokemon.url);
            return {
              name: pokemon.name,
              image: pokeDetails.data.sprites.front_default
            };
          })
        );
        
        this.pokemons = pokemonList;
      } catch (error) {
        console.error(error);
      }
    },
    searchPokemon() {
      
    }
  }
};
</script>

<style>
@import "./assets/styles.scss";
</style>