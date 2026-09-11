<script setup lang="ts">
import { onMounted, onUnmounted } from 'vue'

let channel: BroadcastChannel | null = null
let isRemoteScroll = false

onMounted(() => {
  if (typeof BroadcastChannel === 'undefined') return
  channel = new BroadcastChannel('slidev-scroll-sync')

  channel.onmessage = (event) => {
    if (!event.data) return
    const { slideNo, blockIndex, scrollLeftRatio, scrollTopRatio } = event.data
    isRemoteScroll = true

    // Target the visible/current slide for this slide number
    const slides = Array.from(document.querySelectorAll(`[data-slidev-no="${slideNo}"]`))
    // Pick the main one (not previewNext or thumbnail if in presenter)
    const targetSlide = slides.find(s => !s.closest('.preview-next') && !s.classList.contains('disable-view-transition')) || slides[0]
    
    if (targetSlide) {
      const codeBlocks = targetSlide.querySelectorAll('.slidev-code, pre, .sp-code')
      const target = codeBlocks[blockIndex] as HTMLElement | undefined
      if (target) {
        const maxLeft = target.scrollWidth - target.clientWidth
        const maxTop = target.scrollHeight - target.clientHeight
        if (maxLeft > 0) target.scrollLeft = scrollLeftRatio * maxLeft
        if (maxTop > 0) target.scrollTop = scrollTopRatio * maxTop
      }
    }

    setTimeout(() => {
      isRemoteScroll = false
    }, 60)
  }

  const onScroll = (e: Event) => {
    if (isRemoteScroll) return
    const target = e.target as HTMLElement
    if (!target) return

    // Check if target is a code block or pre
    if (!target.classList?.contains('slidev-code') && target.tagName !== 'PRE' && !target.classList?.contains('sp-code')) {
      return
    }

    const slideWrapper = target.closest('[data-slidev-no]') as HTMLElement | null
    if (!slideWrapper) return
    const slideNo = slideWrapper.getAttribute('data-slidev-no')
    if (!slideNo) return

    const codeBlocks = Array.from(slideWrapper.querySelectorAll('.slidev-code, pre, .sp-code'))
    const blockIndex = codeBlocks.indexOf(target)
    if (blockIndex === -1) return

    const maxLeft = target.scrollWidth - target.clientWidth
    const maxTop = target.scrollHeight - target.clientHeight

    channel?.postMessage({
      slideNo,
      blockIndex,
      scrollLeftRatio: maxLeft > 0 ? target.scrollLeft / maxLeft : 0,
      scrollTopRatio: maxTop > 0 ? target.scrollTop / maxTop : 0,
    })
  }

  window.addEventListener('scroll', onScroll, { capture: true, passive: true })

  onUnmounted(() => {
    window.removeEventListener('scroll', onScroll, { capture: true })
    channel?.close()
  })
})
</script>

<template>
  <span style="display: none;" />
</template>
