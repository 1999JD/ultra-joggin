<template>
  <div class="metronome" ref="metronome" :style="cssProps"></div>
</template>

<script lang="ts" setup>
defineOptions({
  name: 'metronome-display',
})
// import { gsap, Power0 } from 'gsap'
import { computed, onMounted, reactive, ref, watch } from 'vue'

const props = withDefaults(
  defineProps<{
    bpm: number
    rhymePattern: string
  }>(),
  {
    bpm: 40,
    rhymePattern: '1',
  },
)

const metronome = ref()

const config = reactive<{
  rbHeight: number
  rbAngle: string
  timer?: number
  beatTimers: number[]
  counter: number
  audioCtx?: AudioContext
  gain?: GainNode
}>({
  rbHeight: 0,
  rbAngle: '0rad',
  timer: undefined,
  beatTimers: [],
  counter: 0,
  audioCtx: undefined,
  gain: undefined,
})

const started = ref(false)

const period = computed(() => {
  return Math.floor((60 * 1000) / props.bpm)
})

const beats = computed(() => {
  const scopeBeats = []

  const notes = props.rhymePattern.split(',')
  for (const note of notes) {
    const beat = []

    const length = note.length
    for (let j = 0; j < length; j++) {
      const char = note.charAt(j)
      if (char !== '0') {
        beat.push({
          time: (period.value * j) / length,
          type: char,
        })
      }
    }

    scopeBeats.push(beat)
  }

  return scopeBeats
})

const cssProps = computed(() => {
  return {
    '--ball-radius': '20px',
    '--rotate-box-height': config.rbHeight + 'px',
  }
})

const beep = () => {
  if (!config.audioCtx || !config.gain) {
    return
  }
  const oscillator = config.audioCtx.createOscillator()
  oscillator.connect(config.gain)
  oscillator.frequency.value = 1000
  oscillator.type = 'sine'
  oscillator.start(0)
  oscillator.stop(config.audioCtx.currentTime + 0.008)
}
const start = () => {
  started.value = true
  config.counter = 0
  playInOnePeriod()
  config.timer = setInterval(playInOnePeriod, period.value)
}
const stop = () => {
  started.value = false
  config.counter = 0
  clearInterval(config.timer)
  config.timer = undefined
  for (let i = 0; i < config.beatTimers.length; i++) {
    clearTimeout(config.beatTimers[i])
  }
}
const playInOnePeriod = () => {
  const which = config.counter % beats.value.length
  for (const beat of beats.value[which]) {
    config.beatTimers = []
    const beatTimer = setTimeout(() => {
      beep()
    }, beat.time)
    config.beatTimers.push(beatTimer)
  }

  config.counter = config.counter + 1
}

watch(
  () => props.bpm,
  () => {
    if (started.value) {
      stop()
      start()
    }
  },
)

onMounted(() => {
  config.audioCtx = new AudioContext()
  config.gain = config.audioCtx.createGain()
  config.gain.connect(config.audioCtx.destination)
})

defineExpose({
  start: start,
  stop: stop,
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
