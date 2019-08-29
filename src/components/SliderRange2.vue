<template>
  <div class="slider-range">
    <div class="slider-range--svg">
      <FlowChart
        :range="[rangeMin - 1, rangeMax]"
        :data="flowPrice"
      />
    </div>
    <div class="slider-range--btn" ref="sliderRange">
      <div class="line" ref="selectedSegment"></div>
      <button class="min" ref="btnMin"></button>
      <button class="max" ref="btnMax"></button>
    </div>
    <div class="slider-range--text">
      <template v-if="viewPort != 'mobile'">
        <span
          v-if="
            (rangePrice[0] == 0 && rangePrice[1] === undefined) ||
              (rangePrice[0] === null && rangePrice[1] === null)
          "
        >
          любая цена
        </span>
      </template>
      <span
        v-if="
          (rangePrice[0] != 0 && rangePrice[0] !== null) || viewPort == 'mobile'
        "
      >
        oт
        <input
          type="text"
          v-model.number="rangePrice[0]"
          :disabled="viewPort != 'mobile'"
          @input="changePrice('min')"
          @keypress="isNumber($event)"
          placeholder="0"
        />
        ₽
      </span>
      <span
        v-if="
          (rangePrice[1] !== undefined && rangePrice[1] !== null) ||
            viewPort == 'mobile'
        "
      >
        до
        <input
          type="text"
          v-model.number="rangePrice[1]"
          :disabled="viewPort != 'mobile'"
          @input="changePrice('max')"
          @keypress="isNumber($event)"
          :placeholder="price[price.length-1] + ' +'"
        />
        ₽
      </span>
    </div>
  </div>
</template>
<script>
  // ближайшее число из массива
  function nearPrice(arrayRange, elem) {
    return arrayRange.reduce(function(prev, curr) {
      return Math.abs(curr - elem) < Math.abs(prev - elem) ? curr : prev;
    });
  }

  import FlowChart from "./FlowChart.vue";
  import _ from "lodash";

  export default {
    name: "SliderRange1",
    components: {
      FlowChart
    },
    props: {
      price: {
        type: Array
      },
      flowPrice: {
        type: Array
      }
    },
    created() {
      // if (this.$route.query.price) {
      //   let price = this.$route.query.price.split(",").map(Number);
      //
      //   if (price[0] != 0) {
      //     let priceMinVal = nearPrice(this.price, price[0]);
      //     let priceMinIndex = this.price.findIndex(item => item == priceMinVal);
      //     this.rangeSlider[0] = priceMinIndex;
      //     this.rangePrice[0] = price[0];
      //   } else {
      //     this.rangeSlider[0] = 0;
      //   }
      //
      //   if (price[1] != 0) {
      //     let priceMaxVal = nearPrice(this.price, price[1]);
      //     let priceMaxIndex = this.price.findIndex(item => item == priceMaxVal);
      //     this.rangeSlider[1] = priceMaxIndex;
      //     this.rangePrice[1] = price[1];
      //   } else {
      //     this.rangeSlider[1] = this.maxRange;
      //   }
      // }
    },
    mounted() {

      let thisEl = this;
      let sliderWr = this.$refs.sliderRange;
      let btnMin = this.$refs.btnMin;
      let btnMax = this.$refs.btnMax;
      let btnDragMin = false;
      let btnDragMax = false;

      function getCoords(elem) {
        let box = elem.getBoundingClientRect();
        return {
          top: box.top + pageYOffset,
          left: box.left + pageXOffset
        };
      }

      // нажатие на кнопку слайдера
      function downRange(event, btn, slider, type) {
        let buttonCoords = getCoords(btn);
        let shiftX = event.pageX - buttonCoords.left;
        let sliderCoords = getCoords(slider);
        let stepSliderWidth = Math.floor(slider.offsetWidth / thisEl.maxRange);
        if (type=="min") {
          btnDragMin = true;
        }
        if (type=="max") {
          btnDragMax = true;
        }
        return {
          sliderCoords,
          stepSliderWidth,
          shiftX
        }
      };

      // движение кнопки слайдера для минимального значения
      function moveRange(event, btn, slider, sliderCoords, stepSliderWidth, shiftX) {
        if (btnDragMin) {
          let left = event.pageX - shiftX - sliderCoords.left;
          left = Math.round(left / stepSliderWidth) * stepSliderWidth;
          if (left < 0) {
            left = 0;
          }
          let right = slider.offsetWidth - btn.offsetWidth;
          if (left > right) {
            left = right;
          }
          thisEl.rangeMin = thisEl.maxRange - Math.floor((slider.offsetWidth - left) / stepSliderWidth);
          if (thisEl.rangeMin >= thisEl.rangeMax) {
            thisEl.rangeMin = thisEl.rangeMax - 1;
          } else {
            btn.style.left = left + 'px';
            thisEl.priceInput();
          }
        }
      };

      // движение кнопки слайдера для максимального значения
      function moveRange2(event, btn, slider, sliderCoords, stepSliderWidth, shiftX) {
        if (btnDragMax) {
          let left = event.pageX - shiftX - sliderCoords.left;
          left = Math.round(left / stepSliderWidth) * stepSliderWidth;
          if (left < 0) {
            left = 0;
          }
          let right = slider.offsetWidth - btn.offsetWidth;
          if (left > right) {
            left = right;
          }
          thisEl.rangeMax = thisEl.maxRange - Math.floor((slider.offsetWidth - left) / stepSliderWidth);
          if (thisEl.rangeMax <= thisEl.rangeMin) {
            thisEl.rangeMax = thisEl.rangeMin + 1;
          } else {
            btnMax.style.left = left + 'px';
            thisEl.priceInput();
          }
        }
      };

      // обращение к кнопки слайдера (min) - desktop
      btnMin.addEventListener("mousedown",function(evt){
        var down = downRange(evt, btnMin, sliderWr, 'min');
        document.addEventListener("mousemove",function(evt){
          moveRange(evt, btnMin, sliderWr, down.sliderCoords, down.stepSliderWidth, down.shiftX, 'min');
        });
        document.addEventListener("mouseup",function(evt){
          btnDragMin = false;
        });
      });

      // обращение к кнопки слайдера (max) - desktop
      btnMax.addEventListener("mousedown",function(evt){
        var down = downRange(evt, btnMax, sliderWr, 'max');
        document.addEventListener("mousemove",function(evt){
          moveRange2(evt, btnMax, sliderWr, down.sliderCoords, down.stepSliderWidth, down.shiftX, 'max');
        });
        document.addEventListener("mouseup",function(evt){
          btnDragMax = false;
        });
      });

      // обращение к кнопки слайдера (min) - mobile
      btnMin.addEventListener("touchstart",function(evt){
        var down = downRange(evt, btnMin, sliderWr, 'min');
        document.addEventListener("touchmove",function(evt){
          moveRange(evt, btnMin, sliderWr, down.sliderCoords, down.stepSliderWidth, down.shiftX, 'min');
        });
        document.addEventListener("touchend",function(evt){
          btnDragMin = false;
        });
      });

      // обращение к кнопки слайдера (max) - mobile
      btnMax.addEventListener("touchstart",function(evt){
        var down = downRange(evt, btnMax, sliderWr, 'max');
        document.addEventListener("touchmove",function(evt){
          moveRange2(evt, btnMax, sliderWr, down.sliderCoords, down.stepSliderWidth, down.shiftX, 'max');
        });
        document.addEventListener("touchend",function(evt){
          btnDragMax = false;
        });
      });





    },
    methods: {
      priceInput() {
        this.rangePrice = [
          this.price[this.rangeMin],
          this.price[this.rangeMax]
        ];
      },
      isNumber: function(evt) {
        evt = evt ? evt : window.event;
        let charCode = evt.which ? evt.which : evt.keyCode;
        if (
          charCode > 31 &&
          (charCode < 48 || charCode > 57) &&
          charCode !== 46
        ) {
          evt.preventDefault();
        } else {
          return true;
        }
      },
      emitPrice() {
        this.$emit("input", [
          this.rangePrice[0],
          this.rangePrice[1] !== undefined ? this.rangePrice[1] : ""
        ]);
      },
      initRange(min, max) {
        let minRange = (min < max) ? min : max;
        let maxRange = (max==this.maxRange) ? max-1 : (max < min) ? min : max;

        let stepSliderWidth = Math.floor(this.$refs.sliderRange.offsetWidth/this.maxRange);
        let btnMinLeft = stepSliderWidth*minRange;
        this.$refs.btnMin.style.left = btnMinLeft+'px';

        let btnMaxLeft = stepSliderWidth*maxRange;
        this.$refs.btnMax.style.left = btnMaxLeft+'px';
      },

      changePrice: _.debounce(function(val) {
        if (val === "min") {
          if (parseInt(this.rangePrice[0]) > parseInt(this.rangePrice[1])) {
            this.rangeMin = this.rangeMax;
            this.rangePrice[0] = this.rangePrice[1];
          }
        }
        if (val == "max") {
          if (parseInt(this.rangePrice[1]) < parseInt(this.rangePrice[0])) {
            this.rangeMax = this.rangeMin;
            this.rangePrice[1] = this.rangePrice[0];
          }
        }

        let minRange = nearPrice(this.price, this.rangePrice[0]);
        let minIndex = this.price.findIndex(item => item == minRange);

        let maxRange = nearPrice(this.price, this.rangePrice[1]);
        let maxIndex =
          this.rangePrice[1] !== undefined && this.rangePrice[1] !== null && this.rangePrice[1] != ""
            ? this.price.findIndex(item => item == maxRange)
            : 15;

        this.rangeMin = minIndex;
        this.rangeMax = maxIndex;

        this.emitPrice();
        this.initRange(this.rangeMin, this.rangeMax);

      }, 700),

      priceRange() {
        let procentMin = (this.rangeMin / this.maxRange) * 100;
        let procentMax = (this.rangeMax / this.maxRange) * 100;

        this.$refs.selectedSegment.style.background = `linear-gradient(to right,
        #d8d8d8 ${procentMin}%,
        #f51449 ${procentMin}%, #f51449 ${procentMax}%,
        #d8d8d8 ${procentMax}%)`;
      }
    },
    watch: {
      rangeSlider: function() {
        //this.priceRange();
      },
      rangeMin: function (val) {
        this.priceRange();
      },
      rangeMax: function (val) {
        this.priceRange();
      },
    },
    data() {
      return {
        maxRange: 15,
        rangeSlider: [0, 15],
        rangePrice: [null, null],
        rangeMin: 0,
        rangeMax: 15,
        viewPort: 'mobile'
      };
    }
  };
</script>
<style lang="scss" scoped>
  .slider-range {
    margin-bottom: 14px;
    @media all and (max-width: 768px) {
      padding: 0 22px;
      margin-bottom: 18px;
    }
    &--svg {
      padding: 0 6px;
      margin: 0 0 -4px 1px;
    }
    &--btn {
      height: 2px;
      background: #f51449;
      position: relative;
      width: 100%;
      .line {
        height: 2px;
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        background: #f51449;
      }
      button {
        cursor: pointer;
        width: 20px;
        height: 20px;
        border-radius: 30px;
        position: absolute;
        top: -10px;
        left: 0;
        background: #fff;
        border: 2px solid #f51449;
        outline-style: none;
        padding: 0;
        margin: 0;
        &.min {
          left: 0;
        }
        &.max {
          left: calc(100% - 20px);
        }
      }
    }
    &--text {
      display: flex;
      font-size: 14px;
      padding-top: 18px;
      input[type="text"],
      input[type="number"] {
        outline-style: none;
        padding: 0;
        margin: 0 4px;
        background: none;
        border: none;
        font-size: 14px;
        width: 38px;
        text-align: center;
        @media all and (max-width: 768px) {
          border: 1px solid #ccc;
          border-radius: 3px;
          width: 74px;
          text-align: left;
          padding: 4px 8px;
          margin: 0 6px;
        }
      }
      input[type="number"] {
        &::-webkit-outer-spin-button,
        &::-webkit-inner-spin-button {
          -webkit-appearance: none;
        }
      }
      span {
        display: flex;
        margin-right: 6px;
        @media all and (max-width: 768px) {
          align-items: center;
        }
      }
      @media all and (max-width: 768px) {
        align-items: center;
        justify-content: space-between;
        padding-top: 28px;
        margin-bottom: 14px;
      }
    }
  }
</style>
