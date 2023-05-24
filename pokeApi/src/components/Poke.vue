<template>
    <div>
      <h1>Pokédex</h1>
      <div>
        Filtrar por tipo:
        <select v-model="tipoSeleccionado">
          <option disabled value="">Seleccione un tipo</option>
          <option v-for="tipo in tipos" :value="tipo">{{ tipo }}</option>
        </select>
      </div>
      <p>{{ pokemonesFavoritos.length }} Pokémon(s) marcados como favoritos</p>
      <div class="pokedex">
        <div v-for="pokemon in pokemonesFiltrados" :key="pokemon.id">
          <PokemonCard :pokemon="pokemon" :favoritos="pokemonesFavoritos" />
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import PokemonCard from "./components/PokemonCard.vue";
  
  export default {
    name: "Pokedex",
    components: {
      PokemonCard,
    },
    data() {
      return {
        pokemones: [],
        tipos: [],
        tipoSeleccionado: "",
        pokemonesFavoritos: [],
        equipo: [],
      };
    },
    created() {
      this.obtenerTipos();
      this.obtenerPokemones();
    },
    computed: {
      pokemonesFiltrados() {
        if (!this.tipoSeleccionado) {
          return this.pokemones;
        }
        return this.pokemones.filter((pokemon) =>
          pokemon.types.includes(this.tipoSeleccionado)
        );
      },
    },
    methods: {
      async obtenerTipos() {
        try {
          const response = await fetch("https://pokeapi.co/api/v2/type");
          const data = await response.json();
          this.tipos = data.results.map((tipo) => tipo.name);
        } catch (error) {
          console.log(error);
        }
      },
      async obtenerPokemones() {
        try {
          const response = await fetch(
            "https://pokeapi.co/api/v2/pokemon?limit=151"
          );
          const data = await response.json();
          const pokemonesPromises = data.results.map((pokemon) =>
            fetch(pokemon.url).then((res) => res.json())
          );
          const pokemones = await Promise.all(pokemonesPromises);
          this.pokemones = pokemones.map((pokemon) => ({
            id: pokemon.id,
            nombre: pokemon.name,
            imagen: pokemon.sprites.front_default,
            tipos: pokemon.types.map((tipo) => tipo.type.name),
            favorito: false,
            equipo: false,
          }));
        } catch (error) {
          console.log(error);
        }
      },
    },
  };
  </script>
  