<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'

const offsetX = ref(0)
const offsetY = ref(12)
const blur = ref(24)
const spread = ref(0)
const alpha = ref(30)
const inner = ref(false)

let channel: BroadcastChannel | null = null
let isRemoteUpdate = false

onMounted(() => {
  if (typeof BroadcastChannel === 'undefined') return
  channel = new BroadcastChannel('slidev-sync-shadow')
  channel.onmessage = (e) => {
    if (!e.data) return
    isRemoteUpdate = true
    offsetX.value = e.data.offsetX
    offsetY.value = e.data.offsetY
    blur.value = e.data.blur
    spread.value = e.data.spread
    alpha.value = e.data.alpha
    inner.value = e.data.inner
    setTimeout(() => {
      isRemoteUpdate = false
    }, 20)
  }
})

onUnmounted(() => {
  channel?.close()
})

watch([offsetX, offsetY, blur, spread, alpha, inner], () => {
  if (isRemoteUpdate || !channel) return
  channel.postMessage({
    offsetX: offsetX.value,
    offsetY: offsetY.value,
    blur: blur.value,
    spread: spread.value,
    alpha: alpha.value,
    inner: inner.value,
  })
})

const shadow = computed(() =>
  `${inner.value ? 'inset ' : ''}${offsetX.value}px ${offsetY.value}px ${blur.value}px ${spread.value}px rgba(0,0,0,${(alpha.value / 100).toFixed(2)})`,
)

const kotlin = computed(() =>
  `Modifier.${inner.value ? 'innerShadow' : 'dropShadow'}(
  shape = RoundedCornerShape(24.dp),
  shadow = Shadow(
    radius = ${blur.value}.dp,
    spread = ${spread.value}.dp,
    offset = DpOffset(${offsetX.value}.dp, ${offsetY.value}.dp),
    color = Color.Black.copy(alpha = ${(alpha.value / 100).toFixed(2)}f),
  )
)`,
)
</script>

<template>
  <div class="sp">
    <div class="sp-preview">
      <div class="sp-stage">
        <div class="sp-card" :style="{ boxShadow: shadow }" />
      </div>
      <label class="sp-toggle">
        <input type="checkbox" v-model="inner" />
        <span>inner shadow</span>
      </label>
    </div>

    <div class="sp-controls">
      <div class="sp-row">
        <span class="sp-label">offset x</span>
        <input type="range" min="-40" max="40" v-model.number="offsetX" />
        <span class="sp-value">{{ offsetX }}</span>
      </div>
      <div class="sp-row">
        <span class="sp-label">offset y</span>
        <input type="range" min="-40" max="40" v-model.number="offsetY" />
        <span class="sp-value">{{ offsetY }}</span>
      </div>
      <div class="sp-row">
        <span class="sp-label">blur</span>
        <input type="range" min="0" max="80" v-model.number="blur" />
        <span class="sp-value">{{ blur }}</span>
      </div>
      <div class="sp-row">
        <span class="sp-label">spread</span>
        <input type="range" min="-20" max="40" v-model.number="spread" />
        <span class="sp-value">{{ spread }}</span>
      </div>
      <div class="sp-row">
        <span class="sp-label">opacity</span>
        <input type="range" min="0" max="100" v-model.number="alpha" />
        <span class="sp-value">{{ alpha }}</span>
      </div>

      <pre class="sp-code"><code>{{ kotlin }}</code></pre>
    </div>
  </div>
</template>

<style scoped>
.sp {
  display: grid;
  grid-template-columns: 1fr 1.15fr;
  gap: 2rem;
  align-items: center;
}

.sp-preview {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
}

/* A black shadow is invisible on the dark slide background, so the card sits on a
   light stage — the same reason design tools preview shadows on a light canvas. */
.sp-stage {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  padding: 3rem 2rem;
  border-radius: 1rem;
  background: #eceff3;
}

.sp-card {
  width: 8.5rem;
  height: 8.5rem;
  border-radius: 1.5rem;
  background: #fff;
}

.sp-toggle {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-size: 0.8rem;
  opacity: 0.8;
  cursor: pointer;
}

.sp-controls {
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
}

.sp-row {
  display: grid;
  grid-template-columns: 5rem 1fr 2.5rem;
  align-items: center;
  gap: 0.75rem;
  font-size: 0.75rem;
}

.sp-label {
  opacity: 0.7;
}

.sp-value {
  opacity: 0.5;
  text-align: right;
  font-variant-numeric: tabular-nums;
}

.sp-row input[type='range'] {
  width: 100%;
  margin: 0;
}

.sp-code {
  margin-top: 0.75rem;
  padding: 0.75rem;
  border-radius: 0.5rem;
  background: rgba(0, 0, 0, 0.25);
  font-size: 0.6rem;
  line-height: 1.35;
  opacity: 0.85;
  overflow-x: auto;
}
</style>
