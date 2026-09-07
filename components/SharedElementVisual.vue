<script setup lang="ts">
import { ref } from 'vue'

const isDetail = ref(false)
function toggle() {
  isDetail.value = !isDetail.value
}
</script>

<template>
  <div class="shared-demo-container my-3">
    <div class="flex items-center justify-between mb-2 px-1">
      <div class="text-xs font-mono opacity-60">
        Interactive Preview:
      </div>
      <button
        @click="toggle"
        class="px-2.5 py-1 text-xs rounded bg-indigo-600 hover:bg-indigo-500 text-white font-medium transition cursor-pointer"
      >
        {{ isDetail ? '← Tap to view List screen' : 'Tap to view Detail screen →' }}
      </button>
    </div>

    <!-- Screen Mockup Frame -->
    <div
      class="screen-frame relative bg-zinc-950 border border-zinc-800 rounded-xl overflow-hidden shadow-2xl transition-all duration-500 cursor-pointer"
      @click="toggle"
      style="height: 250px;"
    >
      <!-- Mode 1: List View -->
      <div
        class="absolute inset-0 p-3.5 flex flex-col transition-opacity duration-300"
        :class="isDetail ? 'opacity-0 pointer-events-none' : 'opacity-100'"
      >
        <div class="text-[11px] font-semibold text-zinc-400 mb-2 tracking-wider uppercase">Feed Screen</div>
        
        <!-- Other dummy item -->
        <div class="h-9 rounded-lg bg-zinc-900 border border-zinc-800/80 mb-2 opacity-40"></div>
        
        <!-- The Shared Card in List -->
        <div
          class="relative p-2.5 rounded-xl border border-indigo-500/40 bg-zinc-900/90 flex items-center gap-3 shadow-lg hover:border-indigo-500 transition"
        >
          <div
            class="w-12 h-12 rounded-lg bg-gradient-to-br from-indigo-500 to-purple-600 flex items-center justify-center text-white font-bold text-base shadow-md shrink-0"
          >
            🎨
          </div>
          <div class="flex-1 min-w-0">
            <div class="text-sm font-semibold text-zinc-100">Android Studio Ladybug</div>
            <div class="text-xs text-zinc-400">key = "cover-42"</div>
          </div>
          <span class="text-xs text-indigo-400 font-mono">sharedElement()</span>
        </div>

        <div class="h-9 rounded-lg bg-zinc-900 border border-zinc-800/80 mt-2 opacity-40"></div>
      </div>

      <!-- Mode 2: Detail View with Shared Element expanded -->
      <div
        class="absolute inset-0 flex flex-col transition-opacity duration-300"
        :class="isDetail ? 'opacity-100' : 'opacity-0 pointer-events-none'"
      >
        <!-- Expanded Hero Image -->
        <div
          class="h-32 w-full bg-gradient-to-br from-indigo-500 to-purple-600 p-3.5 flex flex-col justify-between shadow-xl relative overflow-hidden"
        >
          <div class="flex justify-between items-center text-white">
            <span class="text-[10px] bg-black/40 backdrop-blur-md px-2 py-0.5 rounded-full font-mono">key = "cover-42"</span>
            <span class="text-[11px] opacity-80">Detail Screen</span>
          </div>
          <div class="text-lg font-bold text-white drop-shadow">Android Studio Ladybug</div>
        </div>

        <!-- Detail content -->
        <div class="p-3.5 flex-1 bg-zinc-950">
          <div class="text-xs font-semibold text-zinc-300">Continuous Geometry & Motion</div>
          <div class="text-[11px] text-zinc-400 mt-1 leading-relaxed">
            The thumbnail automatically animates its bounds, size, and position across navigation routes without jumping.
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.screen-frame {
  box-shadow: 0 12px 30px -8px rgba(0, 0, 0, 0.7), 0 0 0 1px rgba(255, 255, 255, 0.05);
}
</style>
