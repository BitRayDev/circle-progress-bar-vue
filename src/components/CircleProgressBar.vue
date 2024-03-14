<template>
  <svg class="circle-progress-bar" preserveAspectRatio="none" viewBox="-25 0 400 400" fill="none"
       xmlns="http://www.w3.org/2000/svg">
    <g id="progress-bar">
      <text id="30%" fill="#EAE452" xml:space="preserve" style="white-space: pre" font-family="Russo One"
            text-anchor="middle" font-size="64"
            letter-spacing="0em"><tspan x="175" y="223.204" ref="label">30%</tspan></text>
      <path id="bg"
            d="M346 201C346 296.545 268.545 374 173 374C77.4547 374 0 296.545 0 201C0 105.455 77.4547 28 173 28C268.545 28 346 105.455 346 201ZM40.482 201C40.482 274.188 99.8123 333.518 173 333.518C246.188 333.518 305.518 274.188 305.518 201C305.518 127.812 246.188 68.482 173 68.482C99.8123 68.482 40.482 127.812 40.482 201Z"
            fill="#4D4464"/>
      <g id="fill" filter="url(#filter0_d_110_8)">
        <path
          ref="fillArc"
          stroke-width="40"
          stroke="#EAE453"/>
      </g>
    </g>
    <defs>
      <filter id="filter0_d_110_8" x="-25" y="0" width="400" height="400" filterUnits="userSpaceOnUse"
              color-interpolation-filters="sRGB">
        <feFlood flood-opacity="0" result="BackgroundImageFix"/>
        <feColorMatrix in="SourceAlpha" type="matrix" values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 127 0"
                       result="hardAlpha"/>
        <feOffset/>
        <feGaussianBlur stdDeviation="14.45"/>
        <feComposite in2="hardAlpha" operator="out"/>
        <feColorMatrix type="matrix" values="0 0 0 0 0.917647 0 0 0 0 0.894118 0 0 0 0 0.32549 0 0 0 0.55 0"/>
        <feBlend mode="normal" in2="BackgroundImageFix" result="effect1_dropShadow_110_8"/>
        <feBlend mode="normal" in="SourceGraphic" in2="effect1_dropShadow_110_8" result="shape"/>
      </filter>
    </defs>
  </svg>

</template>

<script>
import * as TWEEN from '@tweenjs/tween.js'

// Не использую реактивность Vue для лучшей производительности
const tweenedValues = {label: 0, endAngle: 0};

export default {
  props: {
    value: Number,
  },
  computed: {
    normalizedValue: function () {
      if (!this.value || Number.isNaN(this.value))
        return 0;

      if (this.value > 1)
        return 1;

      if (this.value < 0)
        return 0;

      return this.value;
    }
  },
  methods: {
    polarToCartesian(centerX, centerY, radius, angleInDegrees) {
      const angleInRadians = (angleInDegrees - 90) * Math.PI / 180.0;

      return {
        x: centerX + (radius * Math.cos(angleInRadians)),
        y: centerY + (radius * Math.sin(angleInRadians))
      };
    },
    describeArc(x, y, radius, startAngle, endAngle) {
      const start = this.polarToCartesian(x, y, radius, endAngle);
      const end = this.polarToCartesian(x, y, radius, startAngle);

      const largeArcFlag = endAngle - startAngle <= 180 ? "0" : "1";

      const d = [
        "M", start.x, start.y,
        "A", radius, radius, 0, largeArcFlag, 0, end.x, end.y
      ].join(" ");

      return d;
    },
    tweenFillArc(to) {
      const tween = new TWEEN.Tween(tweenedValues, false)
        .to({label: to * 100, endAngle: to * 359.999}, 1000)
        .easing(TWEEN.Easing.Quadratic.InOut)
        .onUpdate(() => {
          // Не использую реактивность Vue для лучшей производительности
          this.$refs.label.innerHTML = `${Math.round(tweenedValues.label)}%`
          this.$refs.fillArc.setAttribute("d", this.describeArc(173, 201, 152, 0, tweenedValues.endAngle));
        })
        .start()

      const animate = (time) => {
        tween.update(time)
        requestAnimationFrame(animate)
      }
      requestAnimationFrame(animate)
    }
  },
  mounted() {
    this.tweenFillArc(this.normalizedValue);
  },
  watch: {
    normalizedValue(newVal) {
      this.tweenFillArc(newVal);
    }
  }
}
</script>

<style scoped>
.circle-progress-bar {
  width: 375px;
  aspect-ratio: 1;

  font-family: "Russo One", sans-serif;
  font-weight: 400;
  font-style: normal;
}
</style>