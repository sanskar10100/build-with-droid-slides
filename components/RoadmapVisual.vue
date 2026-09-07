<script setup lang="ts">
</script>

<template>
  <div class="roadmap-container my-3">
    <div class="grid grid-cols-4 gap-3 text-xs">
      
      <!-- Step 1 -->
      <div class="p-3 rounded-xl bg-zinc-950 border border-zinc-800 flex flex-col justify-between">
        <div>
          <div class="w-6 h-6 rounded-full bg-indigo-600 text-white font-bold flex items-center justify-center text-xs mb-2">
            1
          </div>
          <div class="font-bold text-zinc-100 text-sm">Kotlin First</div>
          <div class="text-[11px] text-zinc-400 mt-1">
            Null safety, Lambdas, Coroutines & Flow.
          </div>
        </div>
        <div class="mt-3 pt-2 border-t border-zinc-800/80 text-[10px] text-indigo-400 font-mono">
          Skip Java & XML
        </div>
      </div>

      <!-- Step 2 -->
      <div class="p-3 rounded-xl bg-zinc-950 border border-zinc-800 flex flex-col justify-between">
        <div>
          <div class="w-6 h-6 rounded-full bg-purple-600 text-white font-bold flex items-center justify-center text-xs mb-2">
            2
          </div>
          <div class="font-bold text-zinc-100 text-sm">Compose & State</div>
          <div class="text-[11px] text-zinc-400 mt-1">
            UI = f(State), remember, Lazy lists & Navigation.
          </div>
        </div>
        <div class="mt-3 pt-2 border-t border-zinc-800/80 text-[10px] text-purple-400 font-mono">
          Master state hoisting
        </div>
      </div>

      <!-- Step 3 -->
      <div class="p-3 rounded-xl bg-zinc-950 border border-zinc-800 flex flex-col justify-between">
        <div>
          <div class="w-6 h-6 rounded-full bg-emerald-600 text-white font-bold flex items-center justify-center text-xs mb-2">
            3
          </div>
          <div class="font-bold text-zinc-100 text-sm">Architecture</div>
          <div class="text-[11px] text-zinc-400 mt-1">
            ViewModels, Repository pattern, Room & Ktor.
          </div>
        </div>
        <div class="mt-3 pt-2 border-t border-zinc-800/80 text-[10px] text-emerald-400 font-mono">
          Follow "Now in Android"
        </div>
      </div>

      <!-- Step 4 -->
      <div class="p-3 rounded-xl bg-zinc-950 border border-zinc-800 flex flex-col justify-between">
        <div>
          <div class="w-6 h-6 rounded-full bg-amber-600 text-white font-bold flex items-center justify-center text-xs mb-2">
            4
          </div>
          <div class="font-bold text-zinc-100 text-sm">Explore Frontiers</div>
          <div class="text-[11px] text-zinc-400 mt-1">
            KMP/CMP for iOS, or On-Device AI with ML Kit.
          </div>
        </div>
        <div class="mt-3 pt-2 border-t border-zinc-800/80 text-[10px] text-amber-400 font-mono">
          Pick one to stand out
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
.roadmap-container {
  box-shadow: 0 8px 24px -6px rgba(0, 0, 0, 0.5);
}
</style>
