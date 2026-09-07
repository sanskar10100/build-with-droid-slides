<script setup lang="ts">
</script>

<template>
  <div class="grid-visual my-1">
    <div class="grid grid-cols-3 gap-2 p-2.5 rounded-xl bg-zinc-950 border border-zinc-800 text-xs">
      <!-- Header spanning 3 cols -->
      <div class="col-span-3 p-2 rounded-lg bg-indigo-950/70 border border-indigo-500/40 text-indigo-200 flex justify-between items-center">
        <span class="font-mono font-medium text-[11px]">area("header") / span = 3</span>
        <span class="text-[9px] bg-indigo-900/80 px-1.5 py-0.5 rounded text-indigo-300">Header / Hero</span>
      </div>

      <!-- Left card spanning 2 rows -->
      <div class="row-span-2 p-2 rounded-lg bg-emerald-950/60 border border-emerald-500/40 text-emerald-200 flex flex-col justify-between">
        <div>
          <div class="font-mono font-medium text-[11px]">span(rows = 2)</div>
          <div class="text-[9px] opacity-75 mt-0.5">Sidebar / Stats</div>
        </div>
        <div class="text-[9px] text-emerald-400 font-mono">gridCell()</div>
      </div>

      <!-- Content block 1 -->
      <div class="col-span-2 p-2 rounded-lg bg-zinc-900 border border-zinc-700/80 text-zinc-200 flex justify-between items-center">
        <span class="font-mono text-[11px]">span(cols = 2)</span>
        <span class="text-[9px] text-zinc-400">Featured Card</span>
      </div>

      <!-- Sub cells -->
      <div class="p-2 rounded-lg bg-zinc-900 border border-zinc-700/80 text-zinc-300 text-center font-mono text-[10px]">
        Cell [1, 1]
      </div>
      <div class="p-2 rounded-lg bg-zinc-900 border border-zinc-700/80 text-zinc-300 text-center font-mono text-[10px]">
        Cell [1, 2]
      </div>
    </div>
    <div class="flex justify-between items-center px-1 mt-1.5 text-[10px] text-zinc-400">
      <span>✅ Clean 2D tracks with gaps</span>
      <span class="text-rose-400/80 line-through">❌ Nesting 4 Rows inside 3 Columns</span>
    </div>
  </div>
</template>

<style scoped>
.grid-visual {
  box-shadow: 0 6px 20px -4px rgba(0, 0, 0, 0.5);
}
</style>
