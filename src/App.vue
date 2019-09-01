<template>
  <div id="app">
    <SliderRange
      :price="priceRub"
      :flowPrice="facet"
      v-model="filters.price"
    />

    <br><br><br><br><br>

    <SliderRange2
      :price="priceRub"
      :flowPrice="facet"
      v-model="filters.price"
    />
  </div>
</template>

<script>
  import SliderRange from "./components/SliderRange";
  import SliderRange2 from "./components/SliderRange2";
  export default {
    name: 'app',
    components: {
      SliderRange,
      SliderRange2
    },
    data() {
      return {
        filters: {
          price: null
        },
        facet: [1700,2400,1400,1200,1100,1000,1200,1300,1200,1400,1700,1600,500,1600],
        priceRub: [0,500, 1000, 1500, 2000, 2500, 3000, 4000, 5000, 6000, 7000, 8000, 10000, 15000, 20000]
      }
    }
  }
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  padding: 100px 0 300px;
}
</style>
