<template>
  <div id="app" class="bg-grey-dark h-screen flex justify-center">
  <div class="w-1/2 mt-6">
    <base-search-select :options="bands" 
                        :filter-function="filterFunction"
                        v-model="brand"/>
  </div>
   
  </div>
</template>

<script>
import BaseSearchSelect from "./components/base-search-select";

export default {
  name: "App",
  components: {
    BaseSearchSelect
  },
  data() {
    return {
      brand: 'Destruction',
      bands: [
        "Anthrax",
        "Dark Angel",
        "Death Angel",
        "Destruction",
        "Exodus",
        "Flotsam and Jetsam",
        "Kreator",
        "Megadeth",
        "Metallica",
        "Overkill",
        "Sepultura",
        "Slayer",
        "Testament"
      ]
    };
  },

  methods: {
    filterFunction(search, options) {
      return options.filter(option =>
        option.toLowerCase().startsWith(search.toLowerCase())
      );
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
