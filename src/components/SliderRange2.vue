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
        <span>
          любая цена
        </span>
      </template>
      <span>
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
      <span>
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
    this.$refs.btnMin.addEventListener("mousedown", this.downBtnMin);
    this.$refs.btnMax.addEventListener("mousedown", this.downBtnMax);
    this.$refs.btnMin.addEventListener("touchstart", this.downBtnMin);
    this.$refs.btnMax.addEventListener("touchstart", this.downBtnMax);
  },
  methods: {



    downBtnMin(event) {
      this.btnDownMin = {
        sliderCoords: this.$refs.sliderRange.getBoundingClientRect().left + pageXOffset,
        stepSliderWidth: Math.floor(this.$refs.sliderRange.offsetWidth / this.rangeMaxVal),
        shiftX: event.pageX - this.$refs.btnMin.getBoundingClientRect().left + pageXOffset
      };

      document.addEventListener("mouseup", this.upBtnMin);
      document.addEventListener("mousemove", this.moveBtnMin);
      document.addEventListener("touchend", this.upBtnMin);
      document.addEventListener("touchmove", this.moveBtnMin);
    },
    moveBtnMin(event) {
      // позиция слева
      var left = event.pageX - this.btnDownMin.shiftX - this.$refs.sliderRange.getBoundingClientRect().left + pageXOffset;
      left = Math.round(left / this.btnDownMin.stepSliderWidth) * this.btnDownMin.stepSliderWidth;
      if (left < 0) {
        left = 0;
      }
      var right = this.$refs.sliderRange.offsetWidth - this.$refs.btnMin.offsetWidth;
      if (left > right) {
        left = right;
      }

      this.rangeMin = this.rangeMaxVal - Math.floor((this.$refs.sliderRange.offsetWidth - left) / this.btnDownMin.stepSliderWidth);

      if (this.rangeMin >= this.rangeMax) {
        this.rangeMin = this.rangeMax - 1;
      } else {
        this.$refs.btnMin.style.left = left + 'px';
      }
    },
    upBtnMin() {
      document.removeEventListener("mousemove",  this.moveBtnMin);
      document.removeEventListener("touchmove",  this.moveBtnMin);
    },



    downBtnMax(event) {
      this.btnDownMax = {
        sliderCoords: this.$refs.sliderRange.getBoundingClientRect().left + pageXOffset,
        stepSliderWidth: Math.floor(this.$refs.sliderRange.offsetWidth / this.rangeMaxVal),
        shiftX: event.pageX - this.$refs.btnMax.getBoundingClientRect().left + pageXOffset
      };

      document.addEventListener("mouseup", this.upBtnMax);
      document.addEventListener("mousemove", this.moveBtnMax);
      document.addEventListener("touchend", this.upBtnMax);
      document.addEventListener("touchmove", this.moveBtnMax);
    },
    moveBtnMax(event) {
      // позиция слева
      var left = event.pageX - this.btnDownMax.shiftX - this.$refs.sliderRange.getBoundingClientRect().left + pageXOffset;
      left = Math.round(left / this.btnDownMax.stepSliderWidth) * this.btnDownMax.stepSliderWidth;
      if (left < 0) {
        left = 0;
      }
      var right = this.$refs.sliderRange.offsetWidth - this.$refs.btnMax.offsetWidth;
      if (left > right) {
        left = right;
      }

      this.rangeMax = this.rangeMaxVal - Math.floor((this.$refs.sliderRange.offsetWidth - left) / this.btnDownMax.stepSliderWidth);

      if (this.rangeMax <= this.rangeMin) {
        this.rangeMax = this.rangeMin + 1;
      } else {
        this.$refs.btnMax.style.left = left + 'px';
      }

    },
    upBtnMax() {
      document.removeEventListener("mousemove",  this.moveBtnMax);
      document.removeEventListener("touchmove",  this.moveBtnMax);
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
  data() {
    return {
      rangeMaxVal: 15,
      rangePrice: [null, null],
      rangeMin: 0,
      rangeMax: 15,
      viewPort: 'mobile',
      btnDownMin: null,
      btnDownMax: null
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
        width: 42px;
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
