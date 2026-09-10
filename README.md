# The State of Android Development

> **Compose · Platform · KMP · AI**  
> A modern developer talk on how Android development has evolved and where it is heading in 2026.

Built with [Slidev](https://sli.dev/)

---

## 🧭 Talk Outline

The presentation is organized into six core sections based on [slides.md](slides.md):

1. **01 · Jetpack Compose — Where the UI toolkit is now**
   - Declarative UI mental model & unidirectional data flow
   - Expressive UI: Custom shadows, mesh gradients, and shared element transitions
   - Navigation 3 (backstack as a list) & 2D Grid layouts
   - Compiler stability, tooling, and runtime performance

2. **02 · The Android Platform — What the platform demands**
   - Modern annual release cadence & responsive Window Size Classes
   - Adaptive multi-pane UI for foldables and tablets
   - Mandatory edge-to-edge rendering & predictive back gestures
   - Ongoing Activities and modern privacy permissions

3. **03 · Kotlin Multiplatform (KMP) — Where the code runs**
   - The cross-platform dilemma & 3 adoption levels (isolated logic, shared logic + native UI, Compose Multiplatform)
   - `expect` / `actual` mechanism and architecture seams
   - Performance realities and production adoption

4. **04 · AI in Android Development**
   - **Part A · On-Device AI:** System AICore, Gemini Nano, AppFunctions (app as an AI tool), and LiteRT-LM
   - **Part B · AI Developer Workflows:** Autonomous agent loops, `android-cli`, Android Skills, headless debugging with `debroid` (JDWP), and prompting rules (root cause vs. symptoms)

5. **05 · Putting It All Together — Architecture in production**
   - Modern Android Architecture (MAD): UI Layer (Compose), Presentation (ViewModel + StateFlow), and Data Layer (Repository + Room/Ktor)

6. **06 · Your Learning Roadmap — Getting started in 2026**
   - 4-phase learning path for students and beginners
   - What legacy tech to skip (XML layouts, old Fragments, manual adapters)
   - Curated learning resources (*Now in Android*, Android Basics with Compose)

---

## 🚀 Getting Started

### Prerequisites
- **Node.js**: `>= 22` (see [.nvmrc](.nvmrc))
- **npm** (or `pnpm` / `yarn` / `bun`)

### Commands

```bash
# Install dependencies
npm install

# Start local dev server with hot reload
npm run dev

# Build static presentation to dist/
npm run build

# Export slides to PDF
npm run export
```

### Shortcuts
- <kbd>Space</kbd> / <kbd>→</kbd> : Next slide / step
- <kbd>←</kbd> : Previous slide / step
- <kbd>P</kbd> : Presenter mode (notes & timer)
- <kbd>O</kbd> : Slide overview grid
- <kbd>D</kbd> : Drawing / annotation mode

---

## 📁 Repository Structure

```
.
├── slides.md         # Main slide deck content & Slidev frontmatter
├── components/       # Custom Vue components (Adaptive, Shadows, Grids, Roadmap, etc.)
├── public/           # Static assets (images, diagrams, QR codes)
├── style.css         # Custom typography and Slidev styling overrides
├── package.json      # Dependencies and scripts
└── README.md
```
