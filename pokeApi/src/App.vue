
<template>
  <div id="app">
    <pokedex />
  </div>

</template>

<script>
// importamos componente
import pokedex from './components/Pokedex.vue';


export default {
  name: 'App',
  // añadimos componente
  components: {
    pokedex,
  },
};
</script>