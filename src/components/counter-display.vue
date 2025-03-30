<template>
  <div>
    {{ formattedTime }}
  </div>
</template>

<script lang="ts" setup>
defineOptions({
  name: 'counter-display',
})
import { ref, watch } from 'vue'

const props = defineProps<{
  started: boolean
}>()

// 用秒數來儲存時間
const startedTimestamp = ref<number>()
const formattedTime = ref<string>('00:00:000')
// 計時器
const timer = ref<number>()

const transformTimestamp = (val: number) => {
  // 將時間差轉換成經過的分、秒、毫秒
  const minutes = Math.floor(val / 60000) // 1 分鐘 = 60,000 毫秒
  const seconds = Math.floor((val % 60000) / 1000) // 1 秒 = 1,000 毫秒
  const milliseconds = Math.floor(val % 1000) // 取毫秒部分

  // 返回格式化的時間字串，確保是 2 位數和 3 位數
  return `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}:${String(milliseconds).padStart(3, '0')}`
}

// 監聽 props.started 來開始或停止計時
watch(
  () => props.started,
  () => {
    if (props.started) {
      startedTimestamp.value = Date.now()
      timer.value = setInterval(() => {
        formattedTime.value = transformTimestamp(Date.now() - startedTimestamp.value!)
      }, 50)
    } else {
      clearInterval(timer.value)
      timer.value = undefined
      formattedTime.value = '00:00:000'
    }
  },
)
</script>

<style scoped>
/* 可根據需要進行樣式調整 */
</style>
