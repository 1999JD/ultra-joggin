<template>
  <div class="flex flex-col justify-center items-center h-[100vh]">
    <div class="flex gap-x-2 items-center">
      <CounterDisplay :started="started" class="text-4xl"> </CounterDisplay>
      <div class="control">
        <button class="block btn-start cursor-pointer" v-if="!started" @click="start()"></button>
        <button class="block btn-stop cursor-pointer" v-if="started" @click="stop()"></button>
      </div>
    </div>
    <MetronomeDisplay
      ref="metronomeDisplay"
      class="metronome"
      :bpm="bpm"
      :rhymePattern="rhymePattern"
    ></MetronomeDisplay>
    <div class="flex justify-center items-center gap-4 w-full text-[64px]">
      <button class="text-inherit cursor-pointer" @click="modifyBpm(-10)">-</button>
      <div class="w-50 border-b border-b-gray-400">
        <input v-model.number="bpm" class="w-full text-center" />
      </div>
      <button class="text-inherit cursor-pointer" @click="modifyBpm(10)">+</button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import MetronomeDisplay from '@/components/metronome-display.vue'
import CounterDisplay from '@/components/counter-display.vue'

const metronomeDisplay = ref()

const bpm = ref(160)
const rhymePattern = ref('1')
const started = ref(false)

const modifyBpm = (val: number) => {
  bpm.value = bpm.value + val
}

const start = () => {
  started.value = true
  metronomeDisplay.value.start()
}
const stop = () => {
  started.value = false
  console.log('stop')
  metronomeDisplay.value.stop()
}
</script>

<style>
.control .btn-start {
  border-top: 16px solid transparent;
  border-left: 32px solid #cccccc;
  border-bottom: 16px solid transparent;
  border-right: 0;
  width: 0;
  height: 0;
}
.control .btn-stop::before {
  content: '';
  display: block;
  background-color: #cccccc;
  width: 14px;
  height: 32px;
}
.control .btn-stop {
  display: flex;
  justify-content: space-between;
  width: 32px;
  height: 32px;
}
.control .btn-stop::after {
  content: '';
  display: block;
  background-color: #cccccc;
  width: 14px;
  height: 32px;
}
</style>
