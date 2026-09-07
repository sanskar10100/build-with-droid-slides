<script setup lang="ts">
</script>

<template>
  <div class="arch-container my-2">
    <div class="flex flex-col gap-2 p-3.5 rounded-xl bg-zinc-950 border border-zinc-800 text-xs">
      
      <!-- Layer 1: UI -->
      <div class="p-2.5 rounded-lg bg-indigo-950/60 border border-indigo-500/50 flex items-center justify-between">
        <div>
          <span class="font-bold text-indigo-200">1. UI Layer</span>
          <span class="text-zinc-400 ml-2">(Jetpack Compose / CMP)</span>
        </div>
        <div class="text-[11px] font-mono text-indigo-300">
          UI = f(State) · Emits User Events ↑
        </div>
      </div>

      <!-- Arrow -->
      <div class="flex justify-center text-zinc-500 text-[11px] font-mono">
        ↓ StateFlow (UI State) &nbsp;&nbsp;&nbsp;&nbsp; ↑ Intents / Events
      </div>

      <!-- Layer 2: Presentation / State -->
      <div class="p-2.5 rounded-lg bg-purple-950/60 border border-purple-500/50 flex items-center justify-between">
        <div>
          <span class="font-bold text-purple-200">2. Presentation</span>
          <span class="text-zinc-400 ml-2">(ViewModel + Coroutines)</span>
        </div>
        <div class="text-[11px] font-mono text-purple-300">
          Survives config changes · Holds state
        </div>
      </div>

      <!-- Arrow -->
      <div class="flex justify-center text-zinc-500 text-[11px] font-mono">
        ↓ Calls Business Logic / Suspend Functions
      </div>

      <!-- Layer 3: Data & Domain Layer -->
      <div class="p-2.5 rounded-lg bg-emerald-950/60 border border-emerald-500/50">
        <div class="flex items-center justify-between mb-2">
          <div>
            <span class="font-bold text-emerald-200">3. Data Layer & Services</span>
            <span class="text-zinc-400 ml-2">(Offline-first Repository)</span>
          </div>
          <span class="text-[10px] bg-emerald-900/60 px-2 py-0.5 rounded text-emerald-300">
            Easily shared via KMP
          </span>
        </div>

        <div class="grid grid-cols-3 gap-2 mt-1.5 text-center text-[10px]">
          <div class="p-1.5 rounded bg-zinc-900 border border-zinc-700/60 text-zinc-300">
            <div class="font-semibold text-white">Local Cache</div>
            <div class="opacity-70 mt-0.5 font-mono">Room / DataStore</div>
          </div>
          <div class="p-1.5 rounded bg-zinc-900 border border-zinc-700/60 text-zinc-300">
            <div class="font-semibold text-white">Remote API</div>
            <div class="opacity-70 mt-0.5 font-mono">Ktor / OkHttp</div>
          </div>
          <div class="p-1.5 rounded bg-zinc-900 border border-zinc-700/60 text-zinc-300">
            <div class="font-semibold text-white">On-Device AI</div>
            <div class="opacity-70 mt-0.5 font-mono">ML Kit / Gemini Nano</div>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
.arch-container {
  box-shadow: 0 8px 24px -6px rgba(0, 0, 0, 0.5);
}
</style>
