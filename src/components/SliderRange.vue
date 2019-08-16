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
      <div class="line line-cen"></div>
      <div class="line line-start" ref="lineStart"></div>
      <div class="line line-end" ref="lineEnd"></div>
      <div class="slider">
        <input class="min" v-model="min" type="range" max="14" />
        <input class="max" v-model="max" type="range" max="14" />
      </div>
    </div>
    <div class="slider-range--text">
      123123 123sdfsdf
    </div>
  </div>
</template>

<script>
export default {
  name: "SliderRange",
  props: {
    facet: {
      type: Array
    },
    ranges: {
      type: Array
    }
  },
  mounted() {
    this.schedule(this.facet);
    this.setPriceRange(this.price);
  },
  methods: {
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
      this.procentMin = (this.min/this.maxRange)*100;
      this.procentMax = (this.max/this.maxRange)*100;

      this.$refs.lineStart.style.background = 'linear-gradient(to right, #d8d8d8 0%, #d8d8d8 '+this.procentMin +'%, rgba(255,255,255,0) ' + this.procentMin + '%, rgba(255,255,255,0) 100%)';
      this.$refs.lineEnd.style.background = 'linear-gradient(to left, #d8d8d8 0%, #d8d8d8 '+(100-this.procentMax)+'%, rgba(255,255,255,0) ' + (100-this.procentMax) + '%, rgba(255,255,255,0) 100%)'

      let minRange = Math.floor(14 * (this.procentMin/100));
      let maxRange = Math.floor(14 - (14 * (1 - (this.procentMax/100))));

      for (var i = 0; i < this.$refs.red.children.length; i++) {
        if (minRange >= i || i >= maxRange) {
          this.$refs.red.children[i].classList.add('disabled');
        }
        else if (minRange <= i || i <= maxRange) {
          this.$refs.red.children[i].classList.remove('disabled');
        }
      }
    },
    setPriceRange(price) {
      let rangeMin = this.ranges.findIndex(item => item == price[0]);
      let rangeMax = this.ranges.findIndex(item => item == price[1]);

      this.min = rangeMin;
      this.max = rangeMax;
    }
  },
  watch: {
    min: function (val) {
      this.priceRange(val, this.max);
    },
    max: function (val) {
      this.priceRange(this.min, val);
    }
  },
  data() {
    return {
      min: 0,
      max: 14,
      procentMin: 0,
      procentMax: 100,
      maxRange: 14,
      price: [500,6000]
    }
  }
}
</script>

<style lang="scss" scoped>
.slider-range {
  width: 230px;
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
      &.line-cen {
        background: #f51449;
      }
    }
    .slider {
      position: relative;
      width: 100%;

      input[type='range'] {
        width: 100%;
        outline-style: none;
        position: absolute;
        left: 0;
        right: 0;
        margin: 0;
        padding: 0;
        outline-style: none;
      }
      input[type='range']:nth-child(1)::-webkit-slider-thumb{
        z-index: 2;
      }
      input[type='range']::-webkit-slider-runnable-track {
        height: 4px;
        -webkit-appearance: none;
        color: #13bba4;
        margin-top: -1px;
      }

      input[type='range']::-webkit-slider-thumb {
        width: 20px;
        -webkit-appearance: none;
        height: 20px;
        background: #fff;
        border: 2px solid #f51449;
        position: relative;
        z-index: 1;
        margin-top: -7px;
        border-radius: 30px;
      }


      input[type='range'].min::-webkit-slider-thumb {
        //box-shadow: -2000px 0 0 2000px #c00;
        background: #fff;
      }
      input[type='range'].max::-webkit-slider-thumb {
        //box-shadow: -2000px 0 0 2000px #43e5f7;
        background: #fff;
      }


      input[type='range'] {
        -webkit-appearance: none;
        background-color: rgba(255,255,255,0);
      }
      input[type="range"]::-moz-range-progress {
        background-color: rgba(255,255,255,0);
      }
      input[type="range"]::-moz-range-track {
        background-color: rgba(255,255,255,0);
      }
      input[type="range"]::-ms-fill-lower {
        background-color: rgba(255,255,255,0);
      }
      input[type="range"]::-ms-fill-upper {
        background-color: rgba(255,255,255,0);
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
