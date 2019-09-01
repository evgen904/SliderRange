<template>
  <div
    class="slider-range"
  >
    <div class="slider-range--svg">
      <FlowChart
        :range="[rangeMin - 1, rangeMax]"
        :data="flowPrice"
      />
    </div>
    <div class="slider-range--btn" :class="'slider-range--btn_'+viewPort" ref="sliderRange">
      <div class="line" ref="selectedSegment"></div>
      <button
        class="min"
        @mousedown="downBtn($event, 'min')"
        @touchstart="downBtn($event, 'min')"
        ref="btnMin"
      ></button>
      <button
        class="max"
        @mousedown="downBtn($event, 'max')"
        @touchstart="downBtn($event, 'max')"
        ref="btnMax"
      ></button>
    </div>
    <div class="slider-range--text" :class="'slider-range--text_'+viewPort">
      <template v-if="viewPort != 'mobile'">
        <template
          v-if="
          (rangePriceMin === undefined && rangePriceMax === undefined) ||
          (rangePriceMin === null && rangePriceMax === null) ||
          (rangePriceMin == 0 && (rangePriceMax === undefined || rangePriceMax === null))
          "
        >
          <span >
            любая цена
          </span>
        </template>
        <template v-else>
          <span v-if="rangePriceMin !== undefined && rangePriceMin !== null && rangePriceMin !== 0">
            oт {{ rangePriceMin }} ₽
          </span>
          <span v-if="rangePriceMax !== undefined && rangePriceMax !== null">
          до {{ rangePriceMax }} ₽
          </span>
        </template>
      </template>
      <template v-else>
        <span>
          oт
          <input
            type="text"
            v-model.number="rangePriceMin"
            @input="changePrice('min')"
            @keypress="isNumber($event)"
            placeholder="0"
          />
          ₽
        </span>
          <span>
          до
          <input
            type="text"
            v-model.number="rangePriceMax"
            @input="changePrice('max')"
            @keypress="isNumber($event)"
            :placeholder="price[price.length-1] + ' +'"
          />
          ₽
        </span>
      </template>
    </div>
  </div>
</template>
<script>
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
  mounted() {
    this.stepSliderWidth = this.$refs.sliderRange.offsetWidth / this.rangeMaxVal;
    this.offsetRightSlider = this.$refs.sliderRange.offsetWidth - this.$refs.btnMin.offsetWidth;
    this.leftCoordsSlider = this.$refs.sliderRange.getBoundingClientRect().left + pageXOffset;
  },
  methods: {
    //
    downBtn(event, val) {
      this.btnClick = val;
      if (val == "min") {
        this.minShiftX = event.pageX - this.$refs.btnMin.getBoundingClientRect().left + pageXOffset;
      }
      if (val == "max") {
        this.maxShiftX = event.pageX - this.$refs.btnMax.getBoundingClientRect().left + pageXOffset;
      }
      document.addEventListener("mousemove", this.moveBtn);
      document.addEventListener("touchmove", this.moveBtn);
      document.addEventListener("mouseup", this.upBtn);
      document.addEventListener("touchend", this.upBtn);
    },
    upBtn() {
      document.removeEventListener("mousemove", this.moveBtn);
      document.removeEventListener("touchmove", this.moveBtn);
      this.emitPrice();
    },
    moveBtn(event) {
      if (this.btnClick=="min") {
        let leftMin = event.pageX - this.minShiftX - this.leftCoordsSlider;
        leftMin = (leftMin < 0) ? 0 : Math.round(leftMin / this.stepSliderWidth) * this.stepSliderWidth;
        if (leftMin > this.offsetRightSlider)
          leftMin = this.offsetRightSlider;

        this.rangeMin = this.rangeMaxVal - Math.floor((this.$refs.sliderRange.offsetWidth - leftMin) / this.stepSliderWidth);
        if (this.rangeMin >= this.rangeMax) {
          this.rangeMin = this.rangeMax - 1;
        } else {
          this.$refs.btnMin.style.left = leftMin + 'px';
          this.rangePriceMin = this.price[this.rangeMin];
        }
      }
      if (this.btnClick=="max") {
        let leftMax = event.pageX - this.maxShiftX - this.leftCoordsSlider;
        leftMax = (leftMax < 0) ? 0 : Math.round(leftMax / this.stepSliderWidth) * this.stepSliderWidth;
        if (leftMax > this.offsetRightSlider)
          leftMax = this.offsetRightSlider;

        this.rangeMax = this.rangeMaxVal - Math.floor((this.$refs.sliderRange.offsetWidth - leftMax) / this.stepSliderWidth);
        if (this.rangeMax <= this.rangeMin) {
          this.rangeMax = this.rangeMin + 1;
        } else {
          this.$refs.btnMax.style.left = leftMax + 'px';
          this.rangePriceMax = this.price[this.rangeMax];
        }
      }
    },

    //
    changePrice: _.debounce(function(val) {
      if (val === "min") {
        if (this.rangePriceMin > this.rangePriceMax && this.rangePriceMax !== null) {
          this.rangeMin = this.rangeMax;
          this.rangePriceMin = this.rangePriceMax;
        }
      }
      if (val == "max") {
        if (this.rangePriceMax < this.rangePriceMin && this.rangePriceMin !== null) {
          this.rangeMax = this.rangeMin;
          this.rangePriceMax = this.rangePriceMin;
        }
      }

      if (this.rangePriceMin !== null) {
        let minRange = this.nearPrice(this.price, this.rangePriceMin);
        let minIndex = this.price.findIndex(item => item == minRange);
        this.rangeMin = minIndex;
      }

      if (this.rangePriceMax !== null && this.rangePriceMax !== undefined) {
        let maxRange = this.nearPrice(this.price, this.rangePriceMax);
        let maxIndex = this.price.findIndex(item => item == maxRange);
        this.rangeMax = maxIndex;
      }
      this.positionRange(this.rangeMin, this.rangeMax);
      this.emitPrice();
    }, 700),


    lineRange() {
      let procentMin = (this.rangeMin / this.rangeMaxVal) * 100;
      let procentMax = (this.rangeMax / this.rangeMaxVal) * 100;

      this.$refs.selectedSegment.style.background = `linear-gradient(to right,
        #d8d8d8 ${procentMin}%,
        #f51449 ${procentMin}%, #f51449 ${procentMax}%,
        #d8d8d8 ${procentMax}%)`;
    },

    // (min, max) ставим в нужное положение в зависимости от цены
    positionRange(min, max) {
      let minRange = (min < max) ? min : max;
      let maxRange = (max==this.rangeMaxVal) ? max-1 : (max < min) ? min : max;

      let btnMinLeft = this.stepSliderWidth*minRange;
      this.$refs.btnMin.style.left = btnMinLeft+'px';

      let btnMaxLeft = this.stepSliderWidth*maxRange;
      this.$refs.btnMax.style.left = btnMaxLeft+'px';
    },

    // ближайшее число из массива
    nearPrice(arrayRange, elem) {
      return arrayRange.reduce(function(prev, curr) {
        return Math.abs(curr - elem) < Math.abs(prev - elem) ? curr : prev;
      });
    },

    // отправка в filter.price
    emitPrice() {
      this.$emit("input", [
        this.rangePriceMin,
        this.rangePriceMax !== undefined && this.rangePriceMax !== null ? this.rangePriceMax : ""
      ]);
    },

    // ввод только цифр
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
  },
  watch: {
    rangeMin() {
      this.lineRange();
    },
    rangeMax() {
      this.lineRange();
    }
  },
  data() {
    return {
      rangeMaxVal: 16,
      rangePriceMin: null,
      rangePriceMax: null,
      rangeMin: 0,
      rangeMax: 15,
      viewPort: 'mobile',
      minShiftX: null,
      maxShiftX: null,
      btnClick: '',
      stepSliderWidth: null,
      offsetRightSlider: null,
      leftCoordsSlider: null
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
      &_mobile2 {
        button {
          width: 30px;
          height: 30px;
          top: -15px;
          &.max {
            left: calc(100% - 30px);
          }
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
        padding: 4px 8px;
        margin: 0 6px;
        background: none;
        border-radius: 3px;
        border: 1px solid #ccc;
        font-size: 14px;
        width: 74px;
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
        align-items: center;
      }
      &_mobile {
        align-items: center;
        justify-content: space-between;
        padding-top: 28px;
        margin-bottom: 14px;
      }
    }
  }
</style>
