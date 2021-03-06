<template>
  <div class="gt-activity-indicator-rolling">
    <div class="rolling-container">
      <svg
        :viewBox="`0 0 ${viewBoxSize} ${viewBoxSize}`"
        :style="{width: `${size}px`, height: `${size}px`, transform: `rotateZ(${rotate}deg)`}"
        preserveAspectRatio="xMidYMid"
        class="gt-activity-indicator-svg rolling"
      >
        <circle
          fill="none"
          :stroke="borderColor"
          :stroke-width="strokeWidth"
          :cx="viewBoxSize/2"
          :cy="viewBoxSize/2"
          :r="radius"
        ></circle>
        <g v-if="!$slots.circle" class="circle">
          <circle
            v-if="isAutoAnimation || process > 0"
            class="stroke"
            :cx="viewBoxSize/2"
            :cy="viewBoxSize/2"
            :fill="fill"
            :stroke="color"
            :stroke-width="strokeWidth"
            :stroke-dasharray="isAutoAnimation ? `${110 * circlePerimeter / 125}` : strokeDasharray"
            :stroke-linecap="linecap"
            :r="radius"
          >
            <animate
              v-if="isAutoAnimation"
              attributeName="stroke-dashoffset"
              :values="`${360 * circlePerimeter / 125};${140 * circlePerimeter / 125}`"
              dur="2.2s"
              keyTimes="0;1"
              calcMode="spline"
              fill="freeze"
              keySplines="0.41,0.314,0.8,0.54"
              repeatCount="indefinite"
              begin="0"
            ></animate>
            <animateTransform
              v-if="isAutoAnimation"
              :dur="`${duration}s`"
              :values="`0 ${viewBoxSize/2} ${viewBoxSize/2};360 ${viewBoxSize/2} ${viewBoxSize/2}`"
              attributeName="transform"
              type="rotate"
              calcMode="linear"
              keyTimes="0;1"
              begin="0"
              repeatCount="indefinite"
            ></animateTransform>
          </circle>
        </g>
        <slot name="circle" v-else></slot>
        <slot name="defs"></slot>
      </svg>
      <div class="content">
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "gt-activity-indicator-rolling",

  props: {
    size: {
      type: Number,
      default: 70
    },
    width: {
      type: Number
    },
    color: {
      type: String,
      default: "#2F86F6"
    },
    borderColor: {
      type: String,
      default: "rgba(0, 0, 0, .1)"
    },
    fill: {
      type: String,
      default: "transparent"
    },
    linecap: {
      // butt | round | square | inherit
      type: String,
      default: "round"
    },
    rotate: {
      type: Number,
      default: 0
    },
    process: {
      // process control 0-1
      type: Number
    }
  },

  computed: {
    id() {
      return `${this.$options.name}-keyframes-${this.size}`;
    },
    strokeWidth() {
      return this.width || this.size / 12;
    },
    strokeDasharray() {
      return `${this.process * this.circlePerimeter} ${(1 - this.process) *
        this.circlePerimeter}`;
    },
    radius() {
      return this.size / 2;
    },
    viewBoxSize() {
      return this.size + 2 * this.strokeWidth;
    },
    circlePerimeter() {
      return this.size * 3.1415;
    },
    duration() {
      return 2;
    },
    isAutoAnimation() {
      return this.process === undefined;
    }
  }
};
</script>

<style lang="stylus">
.gt-activity-indicator-rolling {
  clearfix();

  .rolling-container {
    position: relative;
    float: left;
  }

  .rolling {
    float: left;

    circle.stroke {
      will-change: auto;
    }
  }

  .content {
    position: absolute;
    absolute-pos();
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>
