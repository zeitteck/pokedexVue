<template>
<!-- si se encuentra en equipo, realiza estilo -->
  <div id="pokemon-card" :class="{ 'en-Equipo': enEquipo }">
    <!-- info del pokemon -->
    <img :src="pokemon.imageUrl" />
    <h2>{{ pokemon.name }}</h2>
    <!--tipos  -->
    <!-- <div>
      <p v-for="tipo in pokemon.tipo" :key="tipo">{{ tipo }}</p>
    </div> -->
    <!-- emit se utiliza para decirle al componente padre que el usuario ha hecho clic en uno de los botones.  -->
    <!-- permite comunicacion entre componente hijo y padre -->
    <button @click="$emit('cambioFavorito', pokemon)">
      <span v-if="enfavorito">Quitar de favoritos</span>
      <span v-else>Favorito</span>
    </button>
    <button @click="$emit('cambioEquipo', pokemon)">
      <span v-if="enEquipo">Quitar del equipo</span>
      <span v-else>Añadir al equipo</span>
    </button>
  </div>
</template>

<script>
export default {
  name: "PokemonCard",
  // props se utiliza para pasar datos desde un componente padre a un componente hijo
  props: {
    pokemon: {
      type: Object,
      required: true,
    },
    equipo: {
      type: Array,
      required: true,
    },
    favoritos: {
      type: Array,
      required: true,
    },
  },
  // Usamos computed porque nos ayuda a crear valores que cambian automáticamente cuando cambian los datos de los que dependen.
  computed: {
    // añadimos
    enEquipo() {
     
      console.log("Equipo:", this.equipo); // verificamos con includes si el id del Pokémon está en el array 'equipo'
      return this.equipo.includes(this.pokemon.id);
    },
    enfavorito() {
      
      console.log("Favoritos:", this.favoritos); // verificamos con includes si el id del Pokémon está en el array 'favoritos'
      return this.favoritos.includes(this.pokemon.id);
    },
  },
};
</script>

<style>

#pokemon-card {
  width: 200px;
  margin: 10px;
  text-align: center;
  
}

#pokemon-card.en-Equipo {
  border: 8px solid rgb(209, 217, 209);
  border-radius: 10px;
  background-color: rgb(233, 148, 148);
}
</style>
