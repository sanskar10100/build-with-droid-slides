<script setup lang="ts">
withDefaults(defineProps<{ height?: string; animated?: boolean }>(), {
  height: '260px',
  animated: true,
})
</script>

<template>
  <div
    class="mesh relative rounded-2xl overflow-hidden"
    :class="{ 'mesh-animated': animated }"
    :style="{ height }"
  >
    <div class="blob blob-a" />
    <div class="blob blob-b" />
    <div class="blob blob-c" />
    <div class="blob blob-d" />
  </div>
</template>

<style scoped>
.mesh {
  background: #1a1030;
  filter: blur(0.5px);
}

.blob {
  position: absolute;
  width: 70%;
  height: 70%;
  border-radius: 50%;
  filter: blur(48px);
  opacity: 0.85;
}

.blob-a { background: #ff5f6d; top: -15%; left: -10%; }
.blob-b { background: #ffc371; top: -10%; right: -15%; }
.blob-c { background: #2b86c5; bottom: -20%; left: 5%; }
.blob-d { background: #7b4397; bottom: -15%; right: -10%; }

.mesh-animated .blob-a { animation: drift-a 9s ease-in-out infinite alternate; }
.mesh-animated .blob-b { animation: drift-b 11s ease-in-out infinite alternate; }
.mesh-animated .blob-c { animation: drift-c 10s ease-in-out infinite alternate; }
.mesh-animated .blob-d { animation: drift-d 12s ease-in-out infinite alternate; }

@keyframes drift-a { to { transform: translate(30%, 25%) scale(1.2); } }
@keyframes drift-b { to { transform: translate(-25%, 35%) scale(1.1); } }
@keyframes drift-c { to { transform: translate(35%, -30%) scale(1.25); } }
@keyframes drift-d { to { transform: translate(-30%, -20%) scale(1.15); } }

@media (prefers-reduced-motion: reduce) {
  .blob { animation: none !important; }
}
</style>
