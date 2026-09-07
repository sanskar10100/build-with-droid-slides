<script setup lang="ts">
import { ref } from 'vue'

const currentMode = ref<'compact' | 'medium' | 'expanded'>('compact')
</script>

<template>
  <div class="adaptive-visual my-1">
    <!-- Mode Switcher Buttons -->
    <div class="flex gap-1.5 mb-2">
      <button
        @click="currentMode = 'compact'"
        class="px-2 py-0.5 text-[11px] rounded border transition cursor-pointer"
        :class="currentMode === 'compact' ? 'bg-indigo-600 border-indigo-500 text-white' : 'bg-zinc-900 border-zinc-700 text-zinc-400 hover:text-zinc-200'"
      >
        📱 Compact (&lt;600dp)
      </button>
      <button
        @click="currentMode = 'medium'"
        class="px-2 py-0.5 text-[11px] rounded border transition cursor-pointer"
        :class="currentMode === 'medium' ? 'bg-indigo-600 border-indigo-500 text-white' : 'bg-zinc-900 border-zinc-700 text-zinc-400 hover:text-zinc-200'"
      >
        📖 Medium (600–840dp)
      </button>
      <button
        @click="currentMode = 'expanded'"
        class="px-2 py-0.5 text-[11px] rounded border transition cursor-pointer"
        :class="currentMode === 'expanded' ? 'bg-indigo-600 border-indigo-500 text-white' : 'bg-zinc-900 border-zinc-700 text-zinc-400 hover:text-zinc-200'"
      >
        💻 Expanded (&gt;840dp)
      </button>
    </div>

    <!-- Screen Container Mockup -->
    <div class="p-2.5 rounded-xl bg-zinc-950 border border-zinc-800 text-xs flex items-center justify-center min-h-[165px]">
      
      <!-- Compact Mode: Phone Portrait -->
      <div v-if="currentMode === 'compact'" class="w-44 h-36 rounded-xl border border-zinc-700 bg-zinc-900 p-2 flex flex-col justify-between shadow-lg">
        <div class="flex items-center justify-between pb-1 border-b border-zinc-800 text-[9px] text-zinc-400">
          <span>TopAppBar</span>
          <span>9:41</span>
        </div>
        <div class="space-y-1 my-auto">
          <div class="h-5 rounded bg-indigo-950/60 border border-indigo-800/40 text-[9px] text-indigo-300 flex items-center px-2">
            List Item 1 (Single Pane)
          </div>
          <div class="h-5 rounded bg-zinc-800/60 text-[9px] text-zinc-400 flex items-center px-2">
            List Item 2
          </div>
        </div>
        <div class="h-3.5 rounded bg-zinc-800/80 text-[8px] text-zinc-400 flex items-center justify-around">
          <span>🏠</span><span>🔍</span><span>👤</span>
        </div>
      </div>

      <!-- Medium Mode: Foldable / Tablet Portrait with Navigation Rail -->
      <div v-else-if="currentMode === 'medium'" class="w-72 h-36 rounded-xl border border-zinc-700 bg-zinc-900 p-2 flex gap-2 shadow-lg">
        <!-- Nav Rail -->
        <div class="w-7 rounded-lg bg-zinc-800/80 flex flex-col items-center justify-around text-[10px] py-1">
          <span>🏠</span><span>🔍</span><span>⚙️</span>
        </div>
        <!-- Main content list -->
        <div class="flex-1 flex flex-col justify-between">
          <div class="text-[9px] font-semibold text-zinc-300 pb-1 border-b border-zinc-800">Navigation Rail Layout</div>
          <div class="space-y-1 my-1">
            <div class="h-5 rounded bg-indigo-950/60 border border-indigo-800/40 text-[9px] text-indigo-300 flex items-center px-2">
              Feed Item 1
            </div>
            <div class="h-5 rounded bg-zinc-800/60 text-[9px] text-zinc-400 flex items-center px-2">
              Feed Item 2
            </div>
          </div>
          <div class="text-[8px] text-zinc-500">Auto-shifts bottom bar to side rail</div>
        </div>
      </div>

      <!-- Expanded Mode: Dual-Pane List-Detail -->
      <div v-else class="w-full max-w-md h-36 rounded-xl border border-zinc-700 bg-zinc-900 p-2 flex gap-2 shadow-lg">
        <!-- Nav Rail -->
        <div class="w-7 rounded-lg bg-zinc-800/80 flex flex-col items-center justify-around text-[10px] py-1">
          <span>🏠</span><span>🔍</span><span>⚙️</span>
        </div>
        <!-- List Pane -->
        <div class="w-36 border-r border-zinc-800 pr-1.5 flex flex-col justify-between">
          <div class="text-[9px] font-semibold text-zinc-300">Items (List Pane)</div>
          <div class="space-y-1">
            <div class="h-5 rounded bg-indigo-950/80 border border-indigo-500 text-[9px] text-indigo-200 flex items-center px-1.5 font-medium">
              Selected Item 1 ▸
            </div>
            <div class="h-5 rounded bg-zinc-800/40 text-[9px] text-zinc-400 flex items-center px-1.5">
              Item 2
            </div>
          </div>
          <div class="text-[8px] text-zinc-500">ListDetailPaneScaffold</div>
        </div>
        <!-- Detail Pane -->
        <div class="flex-1 pl-1 flex flex-col justify-between">
          <div class="text-[9px] font-semibold text-zinc-300">Detail View (Active)</div>
          <div class="p-1.5 rounded bg-zinc-950 border border-zinc-800 text-[9px] text-zinc-300 flex-1 my-1">
            Full detail alongside list without navigating away!
          </div>
          <div class="text-[8px] text-emerald-400 font-mono">WindowWidthSizeClass.Expanded</div>
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
.adaptive-visual {
  box-shadow: 0 6px 20px -4px rgba(0, 0, 0, 0.5);
}
</style>
