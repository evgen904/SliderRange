<template>
  <div>
    <svg
      xmlns="http://www.w3.org/2000/svg"
      class="schedule active"
      width="100%"
      height="50"
      shape-rendering="optimizeSpeed"
    >
      <g id="static" transform="scale(1 -1) translate(0 -50)" fill="#D8D8D8">
        <template v-for="(value, index) in values">
          <rect
            :key="index"
            class="bar"
            :x="stepPos(index, values.length)"
            y="0"
            :height="value"
            :width="stepWidth(index, values.length)"
          ></rect>
        </template>
      </g>
      <g id="dynamic" transform="scale(1 -1) translate(0 -50)" fill="#FDD2D2">
        <template v-for="(value, index) in values">
          <rect
            :key="index"
            class="bar"
            v-bind:class="
              range[0] >= index || index >= range[1] ? 'disabled' : ''
            "
            :x="stepPos(index, values.length)"
            y="0"
            :height="value"
            :width="stepWidth(index, values.length)"
          ></rect>
        </template>
      </g>
    </svg>
  </div>
</template>

<script>
  export default {
    name: "FlowChart",
    props: {
      range: {
        type: Array,
        default: () => [0, 1]
      },
      data: {
        type: Array,
        default: () => [...Array(17).keys()].map(() => 0)
      },
      space: {
        type: Number,
        default: 0.9
      }
    },
    mounted() {
      // setImmediate(() => {
      //   this.isStart = false;
      // });
    },
    data() {
      return {
        isStart: true
      };
    },
    computed: {
      // Путь расположения SVG-файла
      min() {
        return Math.min(...this.data);
      },
      max() {
        return Math.max(...this.data);
      },
      values() {
        return this.data.map(
          v => ((((v - this.min) / (this.max - this.min)) * 100) | 0) + "%"
        );
      },
      state() {
        return this.range;
      }
    },
    methods: {
      stepPos(index, length) {
        return this.space / 2 + 100 * (index / length) + "%";
      },
      stepWidth(index, length) {
        return (100 - this.space * length) / length + "%";
      }
    }
  };
</script>

<style lang="scss" scoped>
  svg {
    display: inline-block;
    vertical-align: baseline;
    transition: fill 0.3s, stroke 0.3s;
    rect {
      transition: height 0.5s;
    }
    rect.disabled {
      height: 0;
    }
    &.start {
      .bar {
        height: 0;
      }
    }
  }
</style>
