# The State of Android Development

> A modern developer talk on how Android development has evolved — exploring declarative UI with Jetpack Compose, platform modernization, Kotlin Multiplatform, and on-device AI.

Built with [Slidev](https://sli.dev/).

---

## 🎙️ Talk Overview

**"The Android you'd learn from a 2019 tutorial is not the Android people build today."**

This talk provides a high-level, practical roadmap for developers and college students stepping into Android today. Rather than getting bogged down in low-level API minutiae or legacy XML patterns, it answers three fundamental questions for each major shift:
* **What changed?**
* **Why did it change?**
* **What does it mean for you as a developer?**

### Key Details
- **Duration:** ~45–60 minutes (~50 min content + 10 min Q&A)
- **Target Audience:** College students, early-career engineers, and developers with little-to-moderate Android background
- **Speaker:** Sanskar (Senior Software Engineer at [Roro](https://roro.io))

---

## 🧭 The Four Core Pillars

```
                     Modern Android Stack
┌─────────────────────────────────────────────────────────────┐
│                    Jetpack Compose UI                       │
├─────────────────────────────────────────────────────────────┤
│             Kotlin Multiplatform Shared Logic               │
├─────────────────────────────────────────────────────────────┤
│         Opinionated Platform APIs (Privacy, Battery)        │
├─────────────────────────────────────────────────────────────┤
│       On-Device AI (System GenAI, Gemini Nano, LiteRT)      │
└─────────────────────────────────────────────────────────────┘
```

1. **How We Build UI — Jetpack Compose**
   - The demise of XML layouts, `findViewById`, and view synchronization bugs.
   - Declarative UI principles and unidirectional data flow ("state goes down, events go up").
   - Core concepts: `remember`, `mutableStateOf`, and recomposition.
   - Modern ecosystem: Adaptive layouts for foldables and tablets, Navigation 3, Material 3 Expressive, and performance updates.

2. **What the Platform Demands — Android Platform Evolution**
   - How Android releases operate today: predictable annual releases with continuous feature updates.
   - Platform opinionation: granular privacy permissions, aggressive battery and background execution limits, edge-to-edge rendering, predictive back gestures, and 16 KB memory page sizes.
   - Expanding form factors: phones, foldables, tablets, Wear OS, Android Auto, and Android XR.

3. **Where the Code Runs — Kotlin Multiplatform (KMP)**
   - Tackling the duplicate business logic dilemma across Android and iOS.
   - KMP (shared networking, data, and business logic with native UI) vs. Compose Multiplatform (shared UI).
   - Core multiplatform ecosystem: Ktor, Room, SQLDelight, Coroutines, and Serialization.

4. **What the App Can Do by Itself — On-Device AI**
   - Cloud AI vs. On-Device AI trade-offs: latency, privacy, offline capabilities, and zero per-call cost vs. constrained compute.
   - The integration tiers:
     1. High-level system GenAI APIs (summarization, rewriting, proofreading).
     2. Gemini Nano via Android AICore (system-managed on-device foundation model).
     3. Custom on-device models with LiteRT (formerly TensorFlow Lite) and MediaPipe.

5. **Putting It Together & Getting Started**
   - End-to-end architecture of a modern app.
   - Recommended learning sequence: Kotlin basics → Compose & state → Coroutines & networking → Architecture & data → Specialization (KMP, AI, or form factors).

---

## 🚀 Steps to Run the Slides

### Prerequisites

- **Node.js**: Version `22` or higher (configured in [.nvmrc](.nvmrc))
- **Package Manager**: `npm` (or `pnpm` / `yarn` / `bun`)

### 1. Clone & Install Dependencies

```bash
# Clone the repository
git clone https://github.com/sanskar10100/slides.git
cd slides

# Use the recommended Node version (optional, if using nvm)
nvm use

# Install dependencies
npm install
```

### 2. Start the Development Server

```bash
npm run dev
```

This starts Slidev at `http://localhost:3030` and automatically opens the presentation in your default browser with hot-module replacement (HMR).

### 3. Presentation Navigation & Shortcuts

| Key | Action |
|---|---|
| <kbd>Space</kbd> / <kbd>→</kbd> / <kbd>↓</kbd> | Next slide or animation step |
| <kbd>←</kbd> / <kbd>↑</kbd> | Previous slide or animation step |
| <kbd>P</kbd> | Toggle **Presenter Mode** (speaker notes, timer, next slide preview) |
| <kbd>O</kbd> | Toggle **Overview Grid** of all slides |
| <kbd>D</kbd> | Toggle **Drawing / Annotation Mode** |
| <kbd>F</kbd> | Toggle Fullscreen |

### 4. Build Static Site

To build a standalone static website for hosting (e.g., on GitHub Pages, Vercel, or Netlify):

```bash
npm run build
```

The output will be generated in the `dist/` directory.

### 5. Export to PDF / PNG

To export the slidedeck as a PDF document (powered by `playwright-chromium`):

```bash
npm run export
```

---

## 📁 Repository Structure

```
.
├── slides.md         # Main slide deck content & Slidev frontmatter
├── components/       # Custom Vue components used inside slides
├── public/           # Static assets (images, icons, QR codes, demo videos)
│   ├── images/       # Architectural diagrams, screenshots, QR codes
│   └── videos/       # Pre-recorded fallback demo videos
├── style.css         # Custom styling & presentation overrides
├── package.json      # Dependencies and scripts
└── README.md         # Project documentation & talk overview
```

---

## 👤 Speaker

**Sanskar**
- **Role:** Senior Software Engineer at [Roro](https://roro.io)
- **LinkedIn:** [linkedin.com/in/sanskar10100](https://linkedin.com/in/sanskar10100)
- **GitHub:** [@sanskar10100](https://github.com/sanskar10100)
