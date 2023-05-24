<template>
  <div>
    <h1>Pokédex</h1>
    <!-- apartado para seleccioanr el tipo  -->
    <select v-model="seleccionarTipo">
      <option value="">Todos</option>
      <option v-for="tipo in tipos" :key="tipo" :value="tipo">
        {{ tipo }}
      </option>
    </select>
    <!-- Contador de favoritos -->
    <p>Favoritos: {{ favoritos.length }}</p>
    <!-- Contador de Pokémon en el equipo -->
    <p>Equipo (max.5): {{ equipo.length }}</p>

    <!-- Lista de Pokémon -->
    <!-- Para cada Pokémon, se crea una instancia del componente PokemonCard  -->
    <div class="pokemon-lista">
      <PokemonCard
        v-for="pokemon in pokemonFiltrado"
        :key="pokemon.id"
        :pokemon="pokemon"
        :equipo="equipo"
        :favoritos="favoritos"
        @cambioFavorito="cambioFavorito"
        @cambioEquipo="cambioEquipo"
      />
    </div>
  </div>
</template>

<script>
// importamos componente
import PokemonCard from "./PokemonCard.vue";
export default {
  name: "Pokedex",
  components: {
    PokemonCard,
  },

  data() {
    return {
      listaPokemon: [], // Lista de todos los Pokémon obtenida de la API
      tipos: [], // Lista de tipos de Pokémon
      seleccionarTipo: "", // Tipo seleccionado para filtrar la lista
      favoritos: [], // Lista de Pokémon favoritos
      equipo: [], // Lista de Pokémon en el equipo
    };
  },

  // Usamos computed porque nos ayuda a crear valores que cambian automáticamente cuando cambian los datos de los que dependen.
  computed: {
    pokemonFiltrado() {
      // Filtrar la lista de Pokémon por el tipo seleccionado
      if (this.seleccionarTipo === "") return this.listaPokemon; // devolvemos lista entera
      return this.listaPokemon.filter(
        (
          pokemon // filtramos
        ) => pokemon.tipo.includes(this.seleccionarTipo) // creamos nuevo array segun tipo
      );
    },
  },

  methods: {
    cambioFavorito(pokemon) {
      // Cambiar el estado de favorito de un Pokémon
      if (this.favoritos.includes(pokemon.id)) {
        this.favoritos = this.favoritos.filter((id) => id !== pokemon.id); // si pokemon esta en el array, no se incluye
      } else {
        this.favoritos.push(pokemon.id); // si no está se añade
      }
    },
    cambioEquipo(pokemon) {
      // Añadir o quitar un Pokémon del equipo
      if (this.equipo.includes(pokemon.id)) {
        this.equipo = this.equipo.filter((id) => id !== pokemon.id); // si pokemon esta en el array, no se incluye
      } else {
        if (this.equipo.length >= 5) return; // Máximo 5 Pokémon en el equipo, no se puede pasar del valor
        this.equipo.push(pokemon.id); // si no está se añade
      }
    },
  },

  mounted() {
    // Obtener la lista de tipos de Pokémon desde la API
    fetch("https://pokeapi.co/api/v2/type")
      .then((respuesta) => respuesta.json())
      .then((data) => {
        // map para extraer los nombres de los tipos de los datos recibidos y crear un nuevo array 
        this.tipos = data.results.map((tipo) => tipo.name);     
        console.log("Tipos de Pokémon:", this.tipos); //  tipos de Pokémon obtenidos de la API.
      })
      .catch((error) => {
        console.error("Error al obtener los tipos de Pokémon:", error); // salta error
        
      });

    // Obtener la lista de Pokémon desde la API
    fetch("https://pokeapi.co/api/v2/pokemon?limit=151")
      .then((response) => response.json())
      .then((data) => {
        data.results.forEach((pokemon) => {
          fetch(pokemon.url)
            .then((response) => response.json())
            .then((pokeData) => {
              // Se crea un objeto "objetoPokemon" con los datos necesarios
              const objetoPokemon = {
                id: pokeData.id,
                name: pokeData.name,
                tipo: pokeData.types.map((tipo) => tipo.type.name), // map para extraer los nombres de los tipos  de los datos que tenemos y crear nuevo array
                imageUrl: pokeData.sprites.front_default,
              };
              // Se añade el objeto "objetoPokemon" a la lista `listaPokemon`
              this.listaPokemon.push(objetoPokemon);
            });
        });
        // imprimimos la lista de Pokémon obtenidos de la API.
        console.log("Lista de Pokémon:", this.listaPokemon);
      })
      .catch((error) => {
        console.log("Error fetching API: ", error);
      });
  },
};
</script>

<style>
body {
  background-color: rgb(189, 141, 141);
}

.pokemon-lista {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-gap: 10px;
}
</style>
