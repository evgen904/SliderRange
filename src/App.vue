<template>
  <div id="app">
    <SliderRange
      :facet="facet"
      :price="priceRub"
      :maxRange="15"
    />
  </div>
</template>

<script>
  import SliderRange from "./components/SliderRange";
  export default {
    name: 'app',
    components: {
      SliderRange
    },
    data() {
      return {
        facet: [700,2400,300,1200,500,700,900,800,400,900,600,1100,1200,1400],
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
    margin-top: 60px;
  }
</style>
