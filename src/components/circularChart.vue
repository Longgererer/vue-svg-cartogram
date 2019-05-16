<template>
  <div class="circle">
    <svg height="150" id="svg" width="150">
      <defs>
        <linearGradient
          :id="'linear'+index"
          v-if="config.gradualChange"
          x1="0%"
          x2="100%"
          y1="0%"
          y2="0%"
        >
          <stop
            :key="index"
            :offset="item.percent"
            :stop-color="item.color"
            v-for="(item, index) in config.gradualChange"
          ></stop>
        </linearGradient>
      </defs>
      <g fill="none" transform="translate(75,75) rotate(-90)">
        <circle
          :stroke="config.gradualChange?`url(#linear${index})`:config.strokeBg"
          :style="strokeStyle.stroke"
          cx="0"
          cy="0"
          r="60"
          ref="outCir"
        ></circle>
        <circle :style="strokeStyle.strokeOut" cx="0" cy="0" id="cir" r="60" ref="cir"></circle>
      </g>
    </svg>
    <div id="text">
      <span id="content">{{content}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'circularChart',
  props: {
    config: Object,
    index: Number
  },
  data() {
    return {
      content: '0.00%'
    }
  },
  mounted() {
    setTimeout(() => {
      this.calcStrokeLength()
    }, 100)
  },
  computed: {
    strokeStyle() {
      const config = this.config
      return {
        strokeOut: {
          stroke: config.strokeOutBg,
          strokeWidth: config.strokeOutW
        },
        stroke: {
          strokeWidth: config.strokeW
        }
      }
    }
  },
  methods: {
    calcStrokeLength() {
      const cir = this.$refs.cir
      const outLength = Math.PI * cir.r.baseVal.value * 2
      cir.style.strokeDasharray = outLength
      const length = (outLength / 100) * this.config.percent
      cir.style.strokeDashoffset = length
      let final = null,
        change = null
      let timer = setInterval(() => {
        change = parseInt(getComputedStyle(cir, null).strokeDashoffset)
        final = (change / outLength) * 100
        this.content = final.toFixed(2) + '%'
        if (
          Math.round(parseFloat(this.content)) >=
          Math.floor(this.config.percent)
        ) {
          clearInterval(timer)
          this.content = this.config.percent.toFixed(2) + '%'
        }
      }, 10)
    }
  }
}
</script>

<style scoped>
.circle {
  width: 150px;
  height: 150px;
  position: relative;
  margin: 0 20px;
}
circle:last-child {
  stroke-dashoffset: 0;
  transition: stroke-dashoffset 1s ease-in;
}
svg {
  transform: scale(-1, 1);
}
#text {
  width: 150px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-weight: 600;
  font-size: 20px;
  font-family: 'Microsoft YaHei UI', sans-serif;
  text-align: center;
}
</style>