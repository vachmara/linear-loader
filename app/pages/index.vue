<script lang="ts" setup>
import { gsap } from 'gsap'

const animate = ref(false)

// Refs for DOM elements
const mainSvg = ref<SVGElement>()
const smallSvg = ref<SVGElement>()
const innerCircle = ref<SVGCircleElement>()
const outerCircle = ref<SVGCircleElement>()
const smallInnerCircle = ref<SVGCircleElement>()

const toggle = () => {
  if (!mainSvg.value || !smallSvg.value || !innerCircle.value || !outerCircle.value || !smallInnerCircle.value) {
    return
  }
  animate.value = true
  const tl = gsap.timeline({
    onComplete: () => {
      animate.value = false
    }
  })

  // Main spinning animation for the SVG
  tl.to(mainSvg.value, {
    rotation: 3.5 * 360, // 5 full rotations
    duration: 3.5,
    ease: 'none' // Linear easing for constant spinning speed
  }, 0)

  // Method 1: Using keyframes for intermediate points
  tl.to(smallSvg.value, {
    keyframes: [
      { x: 60, y: -60, z: -80, duration: 0.5 },
      { x: 120, y: -120, z: 0, scale: 1.5, duration: 0.5 },
      { x: 60, y: -60, z: 100, scale: 1.5, duration: 0.5 },
      { x: 0, y: 0, z: 0, scale: 1, duration: 0.5 },
      { x: 60, y: -60, z: -80, duration: 0.5 },
      { x: 110, y: -110, z: 0, scale: 1.5, duration: 0.7 }
    ]
  }, 0)

  // Final transformations
  tl.to([innerCircle.value, outerCircle.value], {
    strokeDasharray: '0',
    duration: 0.5
  }, 3.2)
    .to(innerCircle.value, {
      attr: { r: 28 },
      duration: 0.5

    }, 3.2)
    .to(smallInnerCircle.value, {
      attr: {
        fill: 'orange'
      },
      strokeWidth: 0,
      duration: 0.5
    }, 3.2)
    .to(smallSvg.value, {
      x: 80,
      y: -80,
      z: 100,
      scale: 1,
      duration: 0.5
    }, 3.2)
}

const reset = () => {
  // Guard clause to ensure all refs are available
  if (!mainSvg.value || !smallSvg.value || !innerCircle.value || !outerCircle.value || !smallInnerCircle.value) {
    return
  }

  // Kill any running animations
  gsap.killTweensOf([mainSvg.value, smallSvg.value, innerCircle.value, outerCircle.value, smallInnerCircle.value])

  animate.value = false

  // Reset all values using GSAP for smooth transitions
  gsap.set(mainSvg.value, { rotation: 0 })
  gsap.set(smallSvg.value, { x: 0, y: 0, z: 0, scale: 1 })
  gsap.set(innerCircle.value, { attr: { r: 0 } })
  gsap.set([innerCircle.value, outerCircle.value], { strokeDasharray: '10' })
  gsap.set(smallInnerCircle.value, {
    attr: {
      fill: 'white',
      stroke: 'var(--ui-color-primary-500)'
    }
  })
}
</script>

<template>
  <div class="flex flex-col gap-5 items-center justify-center h-screen w-screen">
    <div class="flex items-center justify-center gap-4">
      <UButton
        label="Toggle Animation"
        :disabled="animate"
        @click="toggle"
      />
      <UButton
        label="Reset Animation"
        :disabled="animate"
        color="neutral"
        @click="reset"
      />
    </div>
    <div
      class="relative"
      style="perspective: 1000px; transform-style: preserve-3d;"
    >
      <svg
        ref="mainSvg"
        width="200"
        height="200"
        style="transform-origin: center;"
      >

        <circle
          ref="outerCircle"
          cx="100"
          cy="100"
          r="40"
          fill="white"
          stroke="var(--ui-color-primary-500)"
          stroke-width="4"
          stroke-dasharray="8"
          stroke-dashoffset="5"
        />
        <circle
          ref="innerCircle"
          cx="100"
          cy="100"
          r="0"
          fill="var(--ui-color-primary-500)"
          class="z-10"
        />
      </svg>
      <svg
        ref="smallSvg"
        class="absolute bottom-4 left-4"
        width="60"
        height="60"
        style="transform-origin: center;"
      >
        <circle
          cx="30"
          cy="30"
          r="22"
          fill="white"
        />
        <circle
          ref="smallInnerCircle"
          cx="30"
          cy="30"
          r="16"
          fill="white"
          stroke="var(--ui-color-primary-500)"
          stroke-width="7"
        />
      </svg>
    </div>
  </div>
</template>
