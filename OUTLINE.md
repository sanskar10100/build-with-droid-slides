# The State of Android Development — Talk Outline

**Length:** 45–60 min (~50 min content + 10 min Q&A)
**Audience:** College students, low Android experience
**Tone:** Simple language, no deep dives, lots of "why this exists" before "what it is"

**Guiding rule:** every section answers three questions — *What changed? Why did it change? What does it mean for you?*

---

## 0. Opening — 4 min

- Title slide
- Who I am (30 sec, keep it short)
- The hook: **"The Android you'd learn from a 2019 tutorial is not the Android people build today."**
  - One visual: 2019 stack vs 2026 stack, side by side
- Roadmap slide: the four shifts — UI, Platform, Reach, Intelligence

**Notes:** Set expectations — this is a map, not a tutorial. They won't leave able to build everything; they'll leave knowing what's worth learning.

---

## 1. Setting the Scene — 4 min

- A very short history, one slide, as a timeline:
  Java + XML → Kotlin (2017) → Coroutines → Compose (2021) → Multiplatform + AI (now)
- Why Android kept changing: phones stopped being just phones (foldables, tablets, watches, cars, headsets)
- The four shifts, stated plainly:
  1. **How we build UI** → Compose
  2. **What the platform demands** → privacy, battery, form factors
  3. **Where the code runs** → KMP
  4. **What the app can do by itself** → on-device AI

---

## 2. Jetpack Compose — 12 min

**The big idea first, features second.**

- The problem with the old way: XML layout + findViewById + manually updating views → bugs when UI and data drift apart
- **Declarative UI in one sentence:** you describe what the screen should look like for a given state; the framework figures out the updates
  - Analogy: a recipe vs a to-do list of edits
- Side-by-side: ~15 lines of XML + Java vs ~5 lines of Compose (same result)
- **State — the one concept to actually remember**
  - `remember`, `mutableStateOf`, recomposition, in plain words
  - "State goes down, events go up"
- What's new / where it's heading (keep light, 1 slide each, no API dumps):
  - Performance work — it's much faster than the early days; the old "Compose is slow" complaint is stale
  - **Navigation 3** — moving between screens, rethought
  - **Adaptive layouts** — one app that reshapes for phone / foldable / tablet
  - **Material 3 Expressive** — the current look and feel
- 🎬 **DEMO 1** — build a tiny interactive screen from scratch (counter → list → state hoisting)
  - Fallback video: `public/videos/demo-compose.mp4`

**Takeaway slide:** Compose is now the default. If you learn one thing, learn state.

---

## 3. The Android Platform — 10 min

- How Android ships now: yearly releases + monthly/quarterly updates; target SDK requirements force everyone forward
- The themes behind almost every recent change:
  - **Privacy** — permissions keep getting narrower and more granular; users grant less, and grant it later
  - **Battery & background** — the OS is aggressive about killing background work; you can't just "run forever"
  - **Predictable UI** — edge-to-edge, predictive back, gesture navigation
  - **Performance & modernization** — e.g. 16 KB memory page sizes; apps must be rebuilt to keep working
  - **More than phones** — foldables, tablets, Wear, Auto, XR
- ⚠️ *Verify current version numbers / API levels against the latest developer site before the talk*
- **What this means for a student:** don't fight the platform. Assume permissions get denied, assume your app gets killed, assume the screen size changes.

**Takeaway slide:** The platform is opinionated now. Working *with* it is most of the job.

---

## 4. Kotlin Multiplatform — 10 min

- The problem, told as a story: your startup has an Android app and an iOS app. Same login rules, same API calls, same validation — written twice, fixed twice, broken differently.
- **KMP in one line:** write the shared logic once in Kotlin, use it natively on both platforms
- The key distinction (students always mix these up — give it its own slide):
  - **KMP** = share the *logic* (networking, data, business rules), keep native UI
  - **Compose Multiplatform** = share the *UI* too
- How it works, gently: `expect` / `actual` in one small example (e.g. getting the platform name or a database path)
- The ecosystem is the real story: Ktor (networking), Room / SQLDelight (storage), coroutines, serialization
- Where it's genuinely used — a slide of real companies/apps, so it doesn't feel experimental
- Honest limits: iOS tooling and debugging are still rougher; it isn't free
- 🎬 **DEMO 2** — one shared module, two apps running side by side (Android + desktop is easiest and safest on stage)
  - Fallback video: `public/videos/demo-kmp.mp4`

**Takeaway slide:** Kotlin is no longer "the Android language." It's a portable one.

---

## 5. On-Device AI — 10 min

- Frame it as a trade-off, not magic:
  - **Cloud model** — smartest, needs internet, costs money per call, data leaves the device
  - **On-device model** — private, offline, free per call, instant — but smaller and less capable
- The layers, simplest to hardest (one slide, a ladder diagram):
  1. **Ready-made GenAI APIs** — summarize, proofread, rewrite, describe an image. A few lines of code, no ML knowledge.
  2. **Gemini Nano via AICore** — the system-provided model, shared across apps
  3. **Your own model** — LiteRT / MediaPipe when you need something custom
- Good tasks vs bad tasks for a small on-device model (concrete examples — summarizing a note: good; answering trivia: bad)
- The catches: not every device supports it, models take space, quality varies — always design a fallback
- 🎬 **DEMO 3** — summarize or rewrite text entirely offline (airplane mode is a great stage trick)
  - Fallback video: `public/videos/demo-ai.mp4`
- ⚠️ *Verify current API names and device support before the talk*

**Takeaway slide:** AI is becoming a normal Android API, not a research project.

---

## 6. Putting It Together — 3 min

- One slide: what a modern Android app looks like end to end
  - Compose UI → shared Kotlin logic → platform APIs → optional on-device AI
- **Learning roadmap for a beginner** (the slide they'll photograph):
  1. Kotlin basics
  2. Compose + state
  3. Coroutines + networking
  4. Architecture & data
  5. *Then* pick a direction: KMP, AI, or a form factor
- Anti-advice: don't try to learn all four pillars at once
- Resources slide: official docs, Now in Android, codelabs, Kotlin/KMP learning path

---

## 7. Close + Q&A — 5–10 min

- One-sentence summary of the talk
- Contact / slides link (QR code)
- Q&A

---

## Production Checklist

- [ ] Three demo videos recorded as fallbacks → `public/videos/`
- [ ] Screenshots: Compose preview, adaptive layout on foldable, KMP project structure, AI demo → `public/images/`
- [ ] Verify all version numbers and API names against current docs
- [ ] Add a QR code to the hosted slides on the final slide
- [ ] Set `duration: 50min` in headmatter for the presenter timer
- [ ] Rehearse: cut Section 3 or 5 first if running long

## Timing Summary

| Section | Min |
|---|---|
| Opening | 4 |
| Setting the scene | 4 |
| Compose | 12 |
| Platform | 10 |
| KMP | 10 |
| On-device AI | 10 |
| Putting it together | 3 |
| **Content total** | **53** |
| Q&A | 5–10 |
