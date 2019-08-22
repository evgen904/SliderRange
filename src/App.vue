<template>
  <div id="app">
    <SliderRange
      :facet="facet"
      :price="priceRub"
      :maxRange="15"
    />


    <div class="bbb">



      <button @click="openPopup(1)">1</button>
      <button @click="openPopup(2)">2</button>
      <button @click="openPopup(3)">3</button>

      <div v-if="activeMod[0] == 1">
        1 Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ipsam, provident.
      </div>
      <div v-if="activeMod[0] == 2">
        2 Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ipsam, provident.
      </div>
      <div v-if="activeMod[0] == 3">
        3 Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ipsam, provident.
      </div>


      {{ activeMod[0] }}



    </div>





  </div>





  <!--<div class="slider-text-input">-->
		<!--<span v-if="(filters.price[0] == 0 || filters.price[0] == undefined) && (slider[0] == 15 || slider[1] == 15) && viewPort!='mobile'">-->
			<!--{{ $t("price_any") }}-->
		<!--</span>-->
    <!--<span v-if="filters.price[0] != 0 && filters.price[0] != undefined || viewPort=='mobile'">-->
			<!--{{ $t("price_from") }} <input type="number" v-model="priceSlider[0]"> {{ $t("price_currency") }}-->
		<!--</span>-->
    <!--<span v-if="slider[1] != 15 && slider[0] != 15 || viewPort=='mobile'">-->
			<!--{{ $t("price_to") }} <input type="number" v-model="priceSlider[1]"> {{ $t("price_currency") }}-->
		<!--</span>-->
  <!--</div>-->



</template>

<script>
  import SliderRange from "./components/SliderRange";
  import Burger from "./components/Burger";
  export default {
    name: 'app',
    components: {
      SliderRange,
      Burger
    },
    methods: {
      openPopup(val) {

        this.activeMod = [val,0];

        console.log(typeof this.activeMod[0]);


      }
    },
    data() {
      return {
        mod1: 1,
        mod2: 2,
        mod3: 3,
        activeMod: [0,0],
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
  .bbb {
    width: 100%;
    max-width: 500px;
    margin: 0 auto;
  }

  .none {
    display: none;
  }
  .show {
    display: block;
  }

  .pagination {
    display: flex;
    padding-left: 0;
    list-style: none;
    border-radius: 0.25rem;
  }
  .pagination li {
    outline: none;
    min-width: 32px;
    background-color: #fff;
    border-color: #d8d8d8;
    cursor: default;
    position: relative;
    float: left;
    line-height: 1.4;
    text-decoration: none;
    border: 1px solid #d8d8d8;
    margin-left: -1px;
  }


  .pagination li:nth-child(4) ~ .disabled + li {
    display: none;
  }



  .pagination li a {
    font-size: 15px;
    line-height: 2;
    display: block;
    text-align: center;
    color: #444;
    text-decoration: none;
    outline: none;
  }



</style>
