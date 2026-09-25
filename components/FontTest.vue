<script setup lang="ts">
import { onMounted, reactive } from 'vue'

const TIMEOUT = 8000
const base = import.meta.env.BASE_URL
const zh = '逻辑之上艺术在场，'

type Row = {
  label: string
  host: string
  kind: 'file' | 'css'
  url: string
  sample: string
  status: 'waiting' | 'ok' | 'fail'
  detail: string
  family: string
}

const rows = reactive<Row[]>([
  { label: 'Bundled with the slides', host: 'same site 本站', kind: 'file',
    url: `${base}fonts/lobster-latin-400-normal.woff2`, sample: 'Beyond Logic, Art in Presence' },
  { label: 'Google Fonts', host: 'fonts.googleapis.com', kind: 'css',
    url: 'https://fonts.googleapis.com/css2?family=Lobster&text=' + encodeURIComponent('Beyond Logic, Art in Presence'), sample: 'Beyond Logic, Art in Presence' },
  { label: 'Google Fonts China', host: 'fonts.googleapis.cn', kind: 'css',
    url: 'https://fonts.googleapis.cn/css2?family=Lobster&text=' + encodeURIComponent('Beyond Logic, Art in Presence'), sample: 'Beyond Logic, Art in Presence' },
  { label: 'Loli mirror', host: 'fonts.loli.net', kind: 'css',
    url: 'https://fonts.loli.net/css2?family=Lobster&text=' + encodeURIComponent('Beyond Logic, Art in Presence'), sample: 'Beyond Logic, Art in Presence' },
  { label: 'jsDelivr CDN', host: 'cdn.jsdelivr.net', kind: 'file',
    url: 'https://cdn.jsdelivr.net/npm/@fontsource/lobster@5/files/lobster-latin-400-normal.woff2', sample: 'Beyond Logic, Art in Presence' },
  { label: 'Chinese webfont · Google', host: 'fonts.googleapis.com', kind: 'css',
    url: 'https://fonts.googleapis.com/css2?family=ZCOOL+KuaiLe&text=' + encodeURIComponent(zh), sample: '逻辑之上，艺术在场' },
  { label: 'Chinese webfont · Google China', host: 'fonts.googleapis.cn', kind: 'css',
    url: 'https://fonts.googleapis.cn/css2?family=ZCOOL+KuaiLe&text=' + encodeURIComponent(zh), sample: '逻辑之上，艺术在场' },
].map((r, i) => ({ ...r, status: 'waiting', detail: '…', family: `fonttest-${i}` } as Row)))

function withTimeout<T>(p: Promise<T>, ms: number): Promise<T> {
  return Promise.race([p, new Promise<T>((_, rej) => setTimeout(() => rej(new Error('timeout')), ms))])
}

async function test(r: Row) {
  r.status = 'waiting'; r.detail = '…'
  const t0 = performance.now()
  try {
    let fontUrl = r.url
    if (r.kind === 'css') {
      const res = await withTimeout(fetch(r.url, { cache: 'no-store' }), TIMEOUT)
      if (!res.ok) throw new Error(`CSS HTTP ${res.status}`)
      const css = await res.text()
      const m = css.match(/url\(([^)]+)\)/)
      if (!m) throw new Error('no font in CSS')
      fontUrl = m[1].replace(/['"]/g, '')
    }
    const face = new FontFace(r.family, `url(${fontUrl})`)
    await withTimeout(face.load(), TIMEOUT)
    document.fonts.add(face)
    r.status = 'ok'
    r.detail = `${Math.round(performance.now() - t0)} ms`
  }
  catch (e: any) {
    r.status = 'fail'
    r.detail = e?.message === 'timeout' ? 'timed out 超时' : 'blocked 失败'
  }
}

function runAll() { rows.forEach(test) }
onMounted(runAll)
</script>

<template>
  <div class="text-sm">
    <table class="w-full">
      <tbody>
        <tr v-for="r in rows" :key="r.family">
          <td class="py-0 pr-3 w-8 text-xl">
            <span v-if="r.status === 'ok'" class="text-green-400">✓</span>
            <span v-else-if="r.status === 'fail'" class="text-red-400">✗</span>
            <span v-else class="opacity-50">…</span>
          </td>
          <td class="py-0 pr-3 leading-tight">
            {{ r.label }}<br><span class="opacity-50 text-xs">{{ r.host }}</span>
          </td>
          <td class="py-0 pr-3 text-xl whitespace-nowrap" :style="{ fontFamily: `'${r.family}', serif` }">
            {{ r.sample }}
          </td>
          <td class="py-0 opacity-60 text-xs whitespace-nowrap">{{ r.detail }}</td>
        </tr>
      </tbody>
    </table>
    <button class="mt-2 px-3 py-1 rounded border border-gray-500 hover:bg-gray-700" @click="runAll">↻ Retry 重试</button>
  </div>
</template>

<style scoped>
td { padding: 3px 10px 3px 0 !important; vertical-align: middle; }
</style>
