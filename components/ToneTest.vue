<script setup lang="ts">
import { ref } from 'vue'

const status = ref('')

function play() {
  const AC = window.AudioContext || (window as any).webkitAudioContext
  if (!AC) { status.value = 'Web Audio not supported in this browser'; return }
  const ctx = new AC()
  const notes = [523.25, 659.25, 783.99]
  notes.forEach((f, i) => {
    const t = ctx.currentTime + i * 0.35
    const osc = ctx.createOscillator()
    const gain = ctx.createGain()
    osc.type = 'sine'
    osc.frequency.value = f
    gain.gain.setValueAtTime(0, t)
    gain.gain.linearRampToValueAtTime(0.3, t + 0.02)
    gain.gain.exponentialRampToValueAtTime(0.001, t + 0.6)
    osc.connect(gain).connect(ctx.destination)
    osc.start(t)
    osc.stop(t + 0.65)
  })
  status.value = 'Playing… 播放中'
  setTimeout(() => { status.value = 'Done. 完成' ; ctx.close() }, 1500)
}
</script>

<template>
  <div class="mt-10 flex items-center gap-6">
    <button
      class="px-6 py-3 text-2xl rounded bg-teal-600 hover:bg-teal-500 text-white"
      @click="play"
    >▶ Play tone 播放</button>
    <span class="opacity-70">{{ status }}</span>
  </div>
</template>
