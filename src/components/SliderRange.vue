<template>
  <div class="slider-range">
    <div class="slider-range--schedule">
      <svg xmlns="http://www.w3.org/2000/svg" width="100%" height="50" shape-rendering="optimizeSpeed">
        <g ref="grey" id="grey" transform="scale(1 -1) translate(0 -50)" fill="#D8D8D8">
          <rect x="1%" y="0" height="100%" width="6.2%"></rect>
          <rect x="8.1%" y="0" height="50%" width="6.2%"></rect>
          <rect x="15.2%" y="0" height="80%" width="6.2%"></rect>
          <rect x="22.4%" y="0" height="80%" width="6.2%"></rect>
          <rect x="29.4%" y="0" height="100%" width="6.2%"></rect>
          <rect x="36.4%" y="0" height="70%" width="6.2%"></rect>
          <rect x="43.4%" y="0" height="80%" width="6.2%"></rect>
          <rect x="50.4%" y="0" height="70%" width="6.2%"></rect>
          <rect x="57.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="64.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="71.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="78.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="85.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="92.4%" y="0" height="0%" width="6.2%"></rect>
        </g>
        <g ref="red" id="red" transform="scale(1 -1) translate(0 -50)" fill="#FDD2D2">
          <rect x="1%" y="0" height="100%" width="6.2%"></rect>
          <rect x="8.1%" y="0" height="50%" width="6.2%"></rect>
          <rect x="15.2%" y="0" height="80%" width="6.2%"></rect>
          <rect x="22.4%" y="0" height="80%" width="6.2%"></rect>
          <rect x="29.4%" y="0" height="100%" width="6.2%"></rect>
          <rect x="36.4%" y="0" height="70%" width="6.2%"></rect>
          <rect x="43.4%" y="0" height="80%" width="6.2%"></rect>
          <rect x="50.4%" y="0" height="70%" width="6.2%"></rect>
          <rect x="57.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="64.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="71.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="78.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="85.4%" y="0" height="0%" width="6.2%"></rect>
          <rect x="92.4%" y="0" height="0%" width="6.2%"></rect>
        </g>
      </svg>
    </div>
    <div class="slider-range--base">
      <div ref="line" class="line"></div>
      <div class="slider">
        <div ref="minRange" class="slider-one">
          <input class="min" @input="changeRange('min')" v-model="rangeSlider[0]" type="range" :max="maxRange" />
        </div>
        <div class="slider-two">
          <input class="max" @input="changeRange('max')" v-model="rangeSlider[1]" type="range" :max="maxRange" />
        </div>
      </div>
    </div>
    <br><br>
    <div>
      <span>
        любая цена
      </span>
      <span>
        <input @input="changePrice('min')" type="text" v-model="rangePrice[0]">
      </span>
      <span>
        <input @input="changePrice('max')" type="text" v-model="rangePrice[1]">
      </span>
    </div>
  </div>
</template>

<script>

// ближайшее число из массива
function nearPrice(arrayRange, elem) {
  return arrayRange.reduce(function(prev, curr) {
    return (Math.abs(curr - elem) < Math.abs(prev - elem) ? curr : prev);
  });
};

import _ from 'lodash'

export default {
  name: "SliderRange",
  props: {
    facet: {
      type: Array
    },
    price: {
      type: Array
    },
    maxRange: {
      type: Number,
      default: 15
    },
    range: {
      type: Array,
      default: () => [0,15]
    }
  },
  created() {
    this.rangeSlider = this.range;
  },
  mounted() {
    this.schedule(this.facet);
  },
  methods: {

    changeRange(val) {
      if (val == "min") {
        if (parseInt(this.rangeSlider[0]) >= parseInt(this.rangeSlider[1])) {
          this.rangeSlider[0] = parseInt(this.rangeSlider[1])-1;
        }
      }
      if (val == "max") {
        if (parseInt(this.rangeSlider[1]) <= parseInt(this.rangeSlider[0])) {
          this.rangeSlider[1] = parseInt(this.rangeSlider[0])+1;
        }
      }
      this.rangePrice = [
        this.price[this.rangeSlider[0]],
        this.price[this.rangeSlider[1]]
      ];
    },

    changePrice: _.debounce( function (val) {
      if (val == "max") {
        if (parseInt(this.rangePrice[1]) < parseInt(this.rangePrice[0])) {
          this.rangePrice[1] = this.rangePrice[0];
        }
      }
      if (val == "min") {
        if (parseInt(this.rangePrice[0]) > parseInt(this.rangePrice[1])) {
          this.rangePrice[0] = this.rangePrice[1];
        }
      }

      let minRange = nearPrice(this.price,this.rangePrice[0]);
      let maxRange = nearPrice(this.price,this.rangePrice[1]);
      let minIndex = this.price.findIndex(item => item == minRange);
      let maxIndex = this.price.findIndex(item => item == maxRange);

      this.rangeSlider = [minIndex,maxIndex];
    },1000),


    schedule(array) {
      const min = Math.min(...array)
      const max = Math.max(...array)
      const grey = this.$refs.grey;
      Array.from(grey.children).map((v, index)=> v.setAttribute('height', (array[index]-min)/(max-min)*100+"%"))
      const red = this.$refs.red;
      red.classList.remove('active');
      Array.from(red.children).map((v, index)=> v.setAttribute('height', (array[index]-min)/(max-min)*100+"%"))
      setTimeout(() => {
        red.classList.add('active');
      },2000)
    },
    priceRange(min, max) {
      this.procentMin = (this.rangeSlider[0]/this.maxRange)*100;
      this.procentMax = (this.rangeSlider[1]/this.maxRange)*100;

      this.$refs.minRange.style.width = `calc(${this.procentMax}% - 20px)`;

      console.log(this.procentMax);

      this.$refs.line.style.background = `linear-gradient(to right,
        #d8d8d8 ${this.procentMin}%,
        #f51449 ${this.procentMin}%, #f51449 ${this.procentMax}%,
        #d8d8d8 ${this.procentMax}%)`;

      let minRange = Math.floor(this.maxRange * (this.procentMin/100));
      let maxRange = Math.floor(this.maxRange - (this.maxRange * (1 - (this.procentMax/100))));

      for (var i = 0; i < this.$refs.red.children.length; i++) {
        if (minRange >= i || i >= maxRange) {
          this.$refs.red.children[i].classList.add('disabled');
        }
        else if (minRange <= i || i <= maxRange) {
          this.$refs.red.children[i].classList.remove('disabled');
        }
      }

    }
  },
  watch: {
    rangeSlider: function (val) {
      this.priceRange(val[0],val[1]);
    }
  },
  data() {
    return {
      procentMin: 0,
      procentMax: 100,
      rangeSlider: [null, null],
      rangePrice: [null, null],
    }
  }
}
</script>

<style lang="scss" scoped>




.gradient {
  width: 1000px;
  height: 200px;
  background: linear-gradient(to right,
    #d8d8d8 40%,
    #f51449 40%, #f51449 90%,
    #d8d8d8 90%)
}



.slider-range {
  width: 230px;
  margin: 0 auto;
  padding-top: 100px;
  &--schedule {
    margin-bottom: -5px;
    svg {
      rect {
        transition: height 1s;
        &.disabled {
          height: 0;
        }
      }
      #red.active {
        rect {
          transition: height 0.5s;
        }
      }
    }
  }
  &--base {
    position: relative;
    .line {
      height: 2px;
      position: absolute;
      top: 1px;
      left: 0;
      width: 100%;
      background: #f51449;
    }
    .slider {
      position: relative;
      width: 100%;


      &-one {
        position: absolute;
        top: 0;
        z-index: 10;
        min-width: 24px;
      }
      &-two {
        position: absolute;
        top: 0;
        width: 100%;
        z-index: 5;
      }



      input[type=range] {
        -webkit-appearance: none;
        margin: 0;
        padding: 0;
        outline-style: none;
        width: 100%;
      }

      input[type=range]:focus {
        outline: none;
      }
      input[type=range]::-webkit-slider-runnable-track {
        width: 100%;
        height: 20px;
        cursor: pointer;
        animate: 0.2s;
        background: transparent;
        border: none;
      }
      input[type=range]::-webkit-slider-thumb {
        border: 2px solid #f51449;
        height: 20px;
        width: 20px;
        z-index: 20;
        border-radius: 30px;
        background: #fff;
        cursor: pointer;
        -webkit-appearance: none;
        margin-top: 0;
      }
      input[type=range]:focus::-webkit-slider-runnable-track {
        background: transparent;
      }
      input[type=range]::-moz-range-track {
        width: 100%;
        height: 20px;
        cursor: pointer;
        animate: 0.2s;
        background: transparent;
        border: none;
      }
      input[type=range]::-moz-range-thumb {
        border: 2px solid #f51449;
        height: 20px;
        width: 20px;
        z-index: 20;
        border-radius: 30px;
        background: #fff;
        cursor: pointer;
      }
      input[type=range]::-ms-track {
        width: 100%;
        height: 20px;
        cursor: pointer;
        animate: 0.2s;
        background: transparent;
        border-color: transparent;
        border-width: 0;
        color: transparent;
      }
      input[type=range]::-ms-fill-lower {
        background: transparent;
        border: none;
        border-radius: 50px;
      }
      input[type=range]::-ms-fill-upper {
        background: #000;
        border: none;
        border-radius: 50px;
      }
      input[type=range]::-ms-thumb {
        border: 2px solid #f51449;
        height: 20px;
        width: 20px;
        z-index: 20;
        border-radius: 30px;
        background: #fff;
        cursor: pointer;
        margin: 0;
      }
      input[type=range]:focus::-ms-fill-lower {
        background: transparent;
      }
      input[type=range]:focus::-ms-fill-upper {
        background: transparent;
      }









    }
  }
  &--text {
    font-size: 14px;
    color: #444444;
    padding: 18px 0 0;
  }
}
</style>


