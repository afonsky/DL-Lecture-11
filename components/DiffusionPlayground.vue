<script setup lang="ts">
import { computed, onMounted, ref, watch } from 'vue'

const canvas = ref<HTMLCanvasElement | null>(null)
const noiseLevel = ref(50)
const reverse = ref(false)
const base = ref<Float32Array | null>(null)
const noise = ref<Float32Array | null>(null)

const signalWeight = computed(() => Math.sqrt(1 - noiseLevel.value / 100))
const noiseWeight = computed(() => Math.sqrt(noiseLevel.value / 100))
const readout = computed(() => `noise ${noiseLevel.value}% · signal weight ${signalWeight.value.toFixed(2)} · noise weight ${noiseWeight.value.toFixed(2)}`)

function randomGenerator(seed: number) {
  let state = seed >>> 0
  return () => {
    state += 0x6D2B79F5
    let value = state
    value = Math.imul(value ^ (value >>> 15), value | 1)
    value ^= value + Math.imul(value ^ (value >>> 7), value | 61)
    return ((value ^ (value >>> 14)) >>> 0) / 4294967296
  }
}

function gaussian(random: () => number) {
  const u = Math.max(random(), Number.EPSILON)
  const v = random()
  return Math.sqrt(-2 * Math.log(u)) * Math.cos(2 * Math.PI * v)
}

function initialize() {
  const element = canvas.value
  if (!element) return
  const size = 64
  const source = document.createElement('canvas')
  source.width = size
  source.height = size
  const context = source.getContext('2d')
  if (!context) return

  context.fillStyle = '#a9d2ec'
  context.fillRect(0, 0, size, size)
  context.fillStyle = '#8dbb72'
  context.fillRect(0, 41, size, size)
  context.fillStyle = '#d9944a'
  context.beginPath()
  context.ellipse(33, 38, 17, 18, 0, 0, 2 * Math.PI)
  context.fill()
  context.beginPath()
  context.moveTo(18, 27)
  context.lineTo(21, 12)
  context.lineTo(30, 23)
  context.moveTo(37, 23)
  context.lineTo(47, 12)
  context.lineTo(49, 29)
  context.fill()
  context.fillStyle = 'white'
  context.beginPath()
  context.ellipse(26, 32, 3, 4, 0, 0, 2 * Math.PI)
  context.ellipse(41, 32, 3, 4, 0, 0, 2 * Math.PI)
  context.fill()
  context.fillStyle = '#202020'
  context.beginPath()
  context.ellipse(27, 33, 1.2, 1.7, 0, 0, 2 * Math.PI)
  context.ellipse(40, 33, 1.2, 1.7, 0, 0, 2 * Math.PI)
  context.fill()
  context.beginPath()
  context.moveTo(32, 40)
  context.lineTo(35, 40)
  context.lineTo(33.5, 43)
  context.fill()

  base.value = new Float32Array(context.getImageData(0, 0, size, size).data)
  const random = randomGenerator(42)
  noise.value = new Float32Array(size * size * 4)
  for (let i = 0; i < size * size; i += 1) {
    for (let channel = 0; channel < 3; channel += 1) {
      noise.value[i * 4 + channel] = gaussian(random)
    }
    noise.value[i * 4 + 3] = 1
  }
  render()
}

function render() {
  const element = canvas.value
  if (!element || !base.value || !noise.value) return
  const context = element.getContext('2d')
  if (!context) return
  const size = 64
  const image = context.createImageData(size, size)
  for (let i = 0; i < size * size * 4; i += 1) {
    const value = signalWeight.value * base.value[i] + noiseWeight.value * 127 * noise.value[i] + noiseWeight.value * 127
    image.data[i] = Math.max(0, Math.min(255, Math.round(value)))
    if (i % 4 === 3) image.data[i] = 255
  }
  element.width = size
  element.height = size
  context.putImageData(image, 0, 0)
}

function setLevel(value: number) {
  noiseLevel.value = value
}

function toggleDirection() {
  reverse.value = !reverse.value
}

onMounted(initialize)
watch(noiseLevel, render)
</script>

<template>
  <div class="diffusion-lab">
    <div class="diffusion-controls">
      <div class="diffusion-group" aria-label="Noise presets">
        <button :class="{ selected: noiseLevel === 0 }" data-level="0" @click="setLevel(0)">Clean data</button>
        <button :class="{ selected: noiseLevel === 50 }" data-level="50" @click="setLevel(50)">50% noise variance</button>
        <button :class="{ selected: noiseLevel === 100 }" data-level="100" @click="setLevel(100)">Pure Gaussian noise</button>
      </div>
      <button class="direction" data-direction @click="toggleDirection">
        {{ reverse ? 'Reverse: noise → data' : 'Forward: data → noise' }}
      </button>
    </div>

    <div class="diffusion-main">
      <div class="diffusion-canvas-wrap">
        <canvas ref="canvas" role="img" aria-label="Synthetic image corrupted by the selected Gaussian noise level" />
        <div class="diffusion-caption">One fixed noise draw · move the slider to replay the path</div>
      </div>
      <div class="diffusion-explanation">
        <div class="diffusion-path">{{ reverse ? 'x_T' : 'x_0' }} <span>→</span> {{ reverse ? 'x_0' : 'x_T' }}</div>
        <div class="diffusion-readout" id="noise-readout">{{ readout }}</div>
        <div v-if="reverse" class="diffusion-message reverse-message">
          A real model learns each denoising update. This demo replays known states; it is not a trained denoiser.
        </div>
        <div v-else class="diffusion-message">
          Training knows the clean image and the added noise. The target is to predict that noise.
        </div>
        <input
          v-model.number="noiseLevel"
          type="range"
          min="0"
          max="100"
          step="1"
          aria-label="Noise variance percentage"
          data-noise-slider
        >
      </div>
    </div>
  </div>
</template>

<style scoped>
.diffusion-lab { color: #23395d; font-family: system-ui, -apple-system, 'Segoe UI', sans-serif; font-size: 13px; }
.diffusion-controls { display: flex; justify-content: center; align-items: center; gap: 12px; flex-wrap: wrap; margin-bottom: 9px; }
.diffusion-group { display: flex; gap: 5px; flex-wrap: wrap; }
.diffusion-controls button { border: 1px solid #c9d1dc; border-radius: 5px; background: white; color: #40516a; padding: 5px 9px; }
.diffusion-controls button.selected { background: #2a78d6; border-color: #2a78d6; color: white; }
.diffusion-controls button.direction { background: #fff7ef; border-color: #ed7d31; color: #9b4b16; font-weight: 650; }
.diffusion-main { display: grid; grid-template-columns: 1.05fr 1fr; gap: 24px; align-items: center; }
.diffusion-canvas-wrap { text-align: center; }
.diffusion-canvas-wrap canvas { width: 100%; height: 320px; display: block; background: #f4f6f8; border-radius: 7px; image-rendering: pixelated; }
.diffusion-caption { color: #777; font-size: 11.5px; margin-top: 4px; }
.diffusion-path { font-size: 30px; font-weight: 750; text-align: center; margin-bottom: 12px; }
.diffusion-path span { color: #ed7d31; margin: 0 12px; }
.diffusion-readout { background: #f4f6f8; border-radius: 6px; padding: 9px; text-align: center; font-variant-numeric: tabular-nums; }
.diffusion-message { margin-top: 12px; border-left: 4px solid #2a78d6; background: #eef5fd; padding: 10px; line-height: 1.45; }
.diffusion-message.reverse-message { border-left-color: #ed7d31; background: #fff7ef; }
.diffusion-explanation input { width: 100%; margin-top: 20px; accent-color: #ed7d31; }
</style>
