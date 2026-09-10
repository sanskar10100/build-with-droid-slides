---
theme: seriph
title: The State of Android Engineering
info: |
  A talk on the current state of Android development —
  Compose, the platform, Kotlin Multiplatform, and AI in Android development.
class: text-center
transition: slide-left
mdc: true
drawings:
  persist: false
---

# The State of Android Engineering

Compose · Platform · KMP · AI

---
layout: two-cols
---

# Hi, I'm Sanskar

**Engineer at [Roro](https://roro.io)**, a product studio.
I've shipped Android apps used by millions of people across edtech, retail and social media.

<v-clicks>

- **2016** — Built my first Android app in Java with XML layouts, `RelativeLayout`
- **Today** — Pure Kotlin and Jetpack Compose
- **Lately** — Sharing production code across platforms with KMP, and exploring on-device AI capabilities.

</v-clicks>

::right::

<div class="flex flex-col items-center justify-center h-full pl-6">

<img
  src="/images/qr-linkedin.svg"
  alt="QR code linking to linkedin.com/in/sanskar10100"
  style="width: 180px; height: 180px;"
  class="rounded-lg shadow-md"
/>

<div class="mt-3 text-sm font-medium opacity-90">linkedin.com/in/sanskar10100</div>

<div class="mt-4 text-xs opacity-70 text-center font-mono">
github.com/sanskar10100<br>
roro.io
</div>

</div>

<!--
Introduce yourself in ~30 seconds.
The key point: highlight the shift from 2016 (manual boilerplate and fragility) to today.
Reassure the college students: Android is no longer the intimidating, fragmented beast it used to be.
-->

---
transition: fade-out
---

# What We'll Cover Today

<div class="grid grid-cols-2 gap-6 mt-6 text-sm">

<div class="p-4 rounded-xl bg-zinc-950/60 border border-zinc-800/80">
  <div class="font-bold text-indigo-400 text-base mb-1">01 · Jetpack Compose</div>
  <div class="text-zinc-300">Modern declarative UI, state management, shared element animations, and 2D Grid layouts.</div>
</div>

<div class="p-4 rounded-xl bg-zinc-950/60 border border-zinc-800/80">
  <div class="font-bold text-emerald-400 text-base mb-1">02 · The Android Platform</div>
  <div class="text-zinc-300">Adaptive layouts for foldables and tablets, mandatory edge-to-edge, and predictive gestures.</div>
</div>

<div class="p-4 rounded-xl bg-zinc-950/60 border border-zinc-800/80">
  <div class="font-bold text-purple-400 text-base mb-1">03 · Kotlin Multiplatform</div>
  <div class="text-zinc-300">Sharing business logic, networking, and UI across Android, iOS, desktop, and web.</div>
</div>

<div class="p-4 rounded-xl bg-zinc-950/60 border border-zinc-800/80">
  <div class="font-bold text-amber-400 text-base mb-1">04 · AI in Android Development</div>
  <div class="text-zinc-300">Part A: On-Device AI (AICore, Gemini Nano) · Part B: AI Developer Workflows (android-cli, Skills, debroid).</div>
</div>

</div>

<!--
Give a clear birds-eye view of the talk.
Let the audience know: this is a conceptual and architectural map, not a dry syntax lecture.
-->

---
layout: section
---

# 01 · Jetpack Compose

Where the UI toolkit is now

---
clicks: 3
---

# Mental Model: Declarative UI

In the traditional View system, you wrote XML layouts and spent half your code writing manual step-by-step updates. Compose replaces that with **pure functions of state**.

<div class="cmp" :class="'cmp-' + Math.min($clicks, 3)">

<div class="pane pane-view">

**The Imperative Way (View System)**

```xml
<!-- res/layout/activity_main.xml -->
<TextView
  android:id="@+id/label"
  android:text="Hello" />
```

```kotlin
// Manual mutation in Activity
val label = findViewById<TextView>(R.id.label)
label.text = "Hi, $name"
```

<div class="pane-aside">You find the view and mutate it. If data and view get out of sync, you get UI bugs.</div>

</div>

<div class="pane pane-compose">

**The Declarative Way (Compose)**

```kotlin
@Composable
fun Greeting(name: String) {
  Text(text = "Hi, $name")
}
```

<div class="pane-aside">You describe what the screen looks like for a given state. When input changes, Compose redraws.</div>

</div>

</div>

<div class="cmp-note" :class="{ 'cmp-note-on': $clicks >= 3 }">

**The golden rule of Compose:** <em>State goes down, events go up.</em> Your UI is simply <code>UI = f(State)</code>.

</div>

<!--
Keep this simple for students:
1. XML + findViewById was imperative (giving instructions step-by-step).
2. Compose is declarative (describing the final output based on state).
3. If you update the state variable, Compose handles the redrawing automatically.
-->

---
clicks: 3
---

# Lists

If you learned Android before 2021, you remember the boilerplate of building a scrolling list.

<div class="cmp cmp-lists" :class="'cmp-' + Math.min($clicks, 3)">

<div class="pane pane-view">

**RecyclerView (The Old Boilerplate)**

```xml
<!-- res/layout/row_item.xml -->
<TextView
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:id="@+id/label"
  android:layout_width="match_parent"
  android:layout_height="wrap_content" />
```

```kotlin
class ItemAdapter(val items: List<String>) :
  RecyclerView.Adapter<ItemAdapter.VH>() {
  class VH(view: View) : RecyclerView.ViewHolder(view) {
    val label: TextView = view.findViewById(R.id.label)
  }
  override fun onCreateViewHolder(parent: ViewGroup, type: Int) =
    VH(LayoutInflater.from(parent.context)
      .inflate(R.layout.row_item, parent, false))
  override fun onBindViewHolder(holder: VH, position: Int) {
    holder.label.text = items[position]
  }
  override fun getItemCount() = items.size
}
```

```kotlin
// In your Activity / Fragment
recyclerView.layoutManager = LinearLayoutManager(this)
recyclerView.adapter = ItemAdapter(names)
```

<div class="pane-aside">And you still needed DiffUtil callbacks just to get smooth insert/delete animations!</div>

</div>

<div class="pane pane-compose">

**LazyColumn (Compose)**

```kotlin
LazyColumn {
  items(names) { name ->
    Text(name)
  }
}
```

<div class="pane-aside">No adapters, no viewholders, no XML inflation. Heterogeneous layouts are easy to add.</div>

</div>

</div>

<!--
Ask the audience: "How many of you have written a RecyclerView adapter?"
Let the contrast speak for itself. All that old adapter code was plumbing that the framework can do for you.
-->

---

# Expressive UI: Custom Shadows

Compose 1.9 introduced a dedicated, customizable shadow framework with **`dropShadow`** and **`innerShadow`**.

```kotlin
Box(
  modifier = Modifier
    .size(120.dp)
    .dropShadow(
      shape = RoundedCornerShape(24.dp),
      shadow = Shadow(radius = 16.dp, color = Color.Black.copy(alpha = 0.25f))
    )
    .innerShadow(
      shape = RoundedCornerShape(24.dp),
      shadow = Shadow(radius = 8.dp, color = Color.White.copy(alpha = 0.4f))
    )
    .background(Color.White, RoundedCornerShape(24.dp))
)
```

<v-clicks>

- **Why it matters:** Previously, Compose only supported Material elevation shadows. Not possible to draw uniformly around egdes.
- **Figma fidelity:** Designers hand you precise Figma specs (soft colored glows, neumorphic bevels, inset pressed states).

</v-clicks>

<!--
Acknowledge that early Compose lacked visual nuance for non-Material designs.
With dropShadow and innerShadow, you can implement exact Figma specs in a single modifier chain.
-->

---

# Interactive: Custom Shadows in Action

<ShadowPlayground class="mt-4" />

<div class="mt-4 text-xs opacity-70">
Credit: sinasamaki.com/new-shadow-api-for-jetpack-compose
</div>

<!--
LIVE DEMO: Built right into the slide.
Drag the sliders to show how radius, spread, color, and blur map directly to Figma properties.
Move through this in ~60 seconds to keep momentum.
-->

---
layout: two-cols
---

# Expressive UI: Mesh Gradients

Gradients that blend across a 2D mesh, not just a straight line.

```kotlin
val rows = 1; val columns = 1 // Simplest mesh: 4 vertices

val gradientPainter = remember {
  MeshGradientPainter(rows, columns) {
    // Parameters: row, col, normalized offset, color
    setVertex(0, 0, Offset(0f, 0f), Color.Red)     // Top-Left
    setVertex(0, 1, Offset(1f, 0f), Color.Blue)    // Top-Right
    setVertex(1, 0, Offset(0f, 1f), Color.Green)   // Bottom-Left
    setVertex(1, 1, Offset(1f, 1f), Color.Yellow)  // Bottom-Right
  }
}

Box(Modifier.aspectRatio(16/9f).fillMaxWidth().paint(gradientPainter))
```

<v-clicks class="text-xs mt-3 space-y-1">

- **Simplest form:** A 1×1 mesh creates 1 patch with 4 corner vertices.
- Shipped officially in **Compose 1.12** via `MeshGradientPainter`.
- Vertex coordinates and colors can be animated dynamically on the GPU.

</v-clicks>

::right::

<div class="pl-4 pt-2 flex flex-col items-center">
  <img
    src="/images/mesh_gradient_basic.png"
    alt="Simple Mesh Gradient sample from official Android documentation"
    class="rounded-xl shadow-xl border border-zinc-800 w-full"
  />
  <div class="text-xs opacity-70 mt-2 text-center">
    Official Android sample: 1×1 mesh (4 corner vertices)
  </div>

  <div class="mt-3 p-3 rounded-xl bg-zinc-950/80 border border-zinc-800 text-[11px] text-zinc-300 leading-relaxed w-full">
    <div class="font-bold text-indigo-400 mb-1">📐 Grid Vertex Formula</div>
    Total vertices = <code>(rows + 1) × (columns + 1)</code>
    <ul class="mt-1 space-y-0.5 text-zinc-400">
      <li>• <strong>1×1 mesh:</strong> (1+1) × (1+1) = <strong>4 vertices</strong> (simplest)</li>
      <li>• <strong>2×2 mesh:</strong> (2+1) × (2+1) = <strong>9 vertices</strong> (3×3 grid)</li>
    </ul>
  </div>
</div>

<!--
Presenter Notes:
- Explain MeshGradientPainter introduced in Compose 1.12.
- Point out this is the simplest possible mesh: 1 row by 1 column, creating a single patch with 4 vertices.
- Mention the formula: total vertices = (rows + 1) * (columns + 1). So if you specify 2 rows and 2 columns, you get a 3x3 grid of 9 vertices.
- Note how each vertex defines an Offset(x, y) normalized (0f..1f) and a Color, rendered via GPU-accelerated drawMesh.
-->

---
layout: center
clicks: 2
class: text-center p-0
---

<div class="relative w-full h-[520px] flex items-center justify-center">

<!-- Step 0 & Step 1: Side-by-side comparison (iOS Prod vs Android Standard Blur Prod) -->
<div v-if="$clicks < 2" class="flex items-center justify-center gap-16">
<div class="flex flex-col items-center">
<img
src="/images/mesh-gradient-app.png"
alt="iOS Production Mesh Gradient"
style="max-height: 480px; width: auto; object-fit: contain;"
class="rounded-xl shadow-2xl border border-zinc-800"
/>
</div>

<div v-click="1" class="flex flex-col items-center">
<img
src="/images/android-standard-blur.png"
alt="Android Production Standard Blur"
style="max-height: 480px; width: auto; object-fit: contain;"
class="rounded-xl shadow-2xl border border-zinc-800"
/>
</div>
</div>

<!-- Step 2: Final reveal of how we actually do it on Android (hides previous two on click 2) -->
<div v-if="$clicks >= 2" class="flex flex-col items-center justify-center animate-fade-in">
<img
src="/images/android-how-we-actually-do-it.png"
alt="How we actually do it on Android"
style="max-height: 480px; width: auto; object-fit: contain;"
class="rounded-xl shadow-2xl border border-zinc-800"
/>
</div>

</div>

<!--
Presenter Notes:
- Initial view (Click 0): iOS Production app using authentic mesh gradient for the dynamic ambient cover background.
- Click 1: Reveal Android with standard blur — explain what happens when we tried to replicate this on Android before mesh gradients (muddy, washed out, high GPU fill rate).
- Click 2: Reveal how we actually built it on Android before first-party mesh gradients: layered ambient composition with color sampling, blurred texture, and contrast scrims!
-->

---
layout: two-cols
---

# Navigation 3: Backstack as a Plain List

Navigation was historically one of the most frustrating parts of Android. **Navigation 3** re-architected it around plain Kotlin collections.

<div class="pr-4 mt-2">

<v-clicks class="text-sm">

- **The Old Pain:** XML navigation graphs, string-based URL routing, opaque fragment transactions, and hidden backstacks.
- **The Navigation 3 Model:** Your backstack is simply a **list of keys that you own**.
- Want to navigate forward? `add(DetailKey(id = 42))`.
- Want to go back? `removeLastOrNull()`.
- Want to clear to home? `clear(); add(HomeKey)`.

</v-clicks>

<div v-click class="mt-4 p-3 rounded-lg bg-indigo-950/40 border border-indigo-800/40 text-xs text-indigo-200">
Because it is a regular list, complex patterns like deep links, conditional login flows, and multi-pane tablets become standard Kotlin list operations.
</div>

</div>

::right::

```kotlin
// Hold your navigation state
val backStack = rememberNavBackStack(HomeKey)

NavDisplay(
  backStack = backStack,
  onBack = { backStack.removeLastOrNull() },
  entryProvider = entryProvider {
    entry<HomeKey> {
      HomeScreen(onOpenDetail = { id ->
        backStack.add(DetailKey(id))
      })
    }
    entry<DetailKey> { key ->
      DetailScreen(id = key.id)
    }
  }
)
```

<!--
Emphasize this to students: you do not need to memorize complex graph APIs anymore.
If you know how to add and remove items from a Kotlin List, you know how Navigation 3 works.
-->

---
layout: two-cols
---

# 2D Layouts: The Compose Grid

Real two-dimensional layouts without nested hierarchy hell

<div class="pr-3">

```kotlin
Grid(columns = 3, rows = 3, gap = 8.dp) {
  // Spans 3 columns for header
  HeaderCard(Modifier.gridCell(columnSpan = 3))

  // Spans 2 rows for sidebar
  SidebarCard(Modifier.gridCell(rowSpan = 2))

  // Remaining cells fill slots
  MetricCard()
  MetricCard()
}
```

<v-clicks class="text-xs space-y-1 mt-2">

- **2D Tracks & Gaps:** Define columns, rows, and gutters directly.
- **Cell Spanning:** Span multiple rows and columns with `gridCell()`.
- **Named Areas:** Place items into named layout areas, just like CSS Grid.

</v-clicks>

</div>

::right::

<div class="pl-2">
  <GridLayoutVisual />
</div>

<!--
Connect this with web knowledge: if students know CSS Grid, Compose Grid will feel immediately familiar.
It completely removes the performance penalty of nested layout passes.
-->

---

# Also Worth Knowing in Modern Compose

Recent developer experience and quality-of-life improvements shipping in 2025/2026:

<div class="grid grid-cols-2 gap-4 mt-6 text-sm">

<a href="https://developer.android.com/develop/ui/compose/text/user-input" target="_blank" class="p-4 rounded-xl bg-zinc-950 border border-zinc-800 hover:border-indigo-500/70 transition block no-underline !decoration-none group">
  <div class="flex items-center justify-between mb-1">
    <div class="font-bold text-indigo-400 font-mono text-xs">TextFieldState</div>
    <span class="text-zinc-600 group-hover:text-indigo-400 text-xs transition">↗</span>
  </div>
  <p class="text-zinc-400 text-xs leading-relaxed">
    Text fields redesigned around explicit state instead of asynchronous <code>value</code>/<code>onValueChange</code> callbacks. Eliminates cursor jumping and race conditions in formatted inputs. Easier to init with exisiting text and place cursor at end.
  </p>
</a>

<a href="https://developer.android.com/develop/ui/compose/state-saving" target="_blank" class="p-4 rounded-xl bg-zinc-950 border border-zinc-800 hover:border-emerald-500/70 transition block no-underline !decoration-none group">
  <div class="flex items-center justify-between mb-1">
    <div class="font-bold text-emerald-400 font-mono text-xs">retain { }</div>
    <span class="text-zinc-600 group-hover:text-emerald-400 text-xs transition">↗</span>
  </div>
  <p class="text-zinc-400 text-xs leading-relaxed">
    Survives activity recreation and screen rotation directly inside the composition tree without having to scaffold a full <code>ViewModel</code> class.
  </p>
</a>

<a href="https://developer.android.com/identity/sign-in/credential-manager" target="_blank" class="p-4 rounded-xl bg-zinc-950 border border-zinc-800 hover:border-purple-500/70 transition block no-underline !decoration-none group">
  <div class="flex items-center justify-between mb-1">
    <div class="font-bold text-purple-400 font-mono text-xs">Credential Manager</div>
    <span class="text-zinc-600 group-hover:text-purple-400 text-xs transition">↗</span>
  </div>
  <p class="text-zinc-400 text-xs leading-relaxed">
    One-tap passkeys and Google Password Manager logins natively integrated directly into Compose input fields with zero boilerplate.
  </p>
</a>

<a href="https://developer.android.com/develop/ui/compose/animation/shared-elements" target="_blank" class="p-4 rounded-xl bg-zinc-950 border border-zinc-800 hover:border-amber-500/70 transition block no-underline !decoration-none group">
  <div class="flex items-center justify-between mb-1">
    <div class="font-bold text-amber-400 font-mono text-xs">Shared Transitions</div>
    <span class="text-zinc-600 group-hover:text-amber-400 text-xs transition">↗</span>
  </div>
  <p class="text-zinc-400 text-xs leading-relaxed">
    Smooth spatial continuity across navigation routes with <code>SharedTransitionLayout</code>. Morphs element bounds and clip shapes instead of abrupt screen cuts.
  </p>
</a>

</div>

<!--
Quick hits slide. Highlight TextFieldState and Shared Transitions:
- TextFieldState: fixes cursor jumping bugs in formatted text at the architectural level.
- Shared Transitions: provides seamless bounds morphing across navigation routes without manual coordinate math.
- All 4 cards link directly to official Android documentation.
-->

---
layout: default
---

# The Official Compose Roadmap

What's done, and what's next:  

<div class="grid grid-cols-2 gap-6 mt-4 text-xs">

<div>
  <div class="font-bold text-emerald-400 text-sm mb-2.5 flex items-center gap-1.5">
    <span>🟢</span>
    <span>Already Shipped (Done)</span>
  </div>
  <ul class="space-y-2.5 text-zinc-300 leading-relaxed">
    <li><strong>Scroll Performance:</strong> Jank and frame pacing on par with <code>RecyclerView</code> (since 1.9).</li>
    <li><strong>Expressive Styling:</strong> Native drop shadows, inner shadows, blur, and mesh gradients.</li>
    <li><strong>LazyList Animations:</strong> Built-in item placement animations and multi-screen Drag &amp; Drop.</li>
    <li><strong>Compiler Defaults:</strong> Strong Skipping mode and stability inference enabled by default.</li>
  </ul>
</div>

<div>
  <div class="font-bold text-amber-400 text-sm mb-2.5 flex items-center gap-1.5">
    <span>🎯</span>
    <span>What's Next (In Focus)</span>
  </div>
  <ul class="space-y-2.5 text-zinc-300 leading-relaxed">
    <li><strong>Startup Performance:</strong> Optimizing cold-start composition and initialization time.</li>
    <li><strong>Built-in Scrollbars:</strong> First-party scrollbars for Lazy layouts and scroll containers.</li>
    <li><strong>GenAI &amp; UI Tooling:</strong> First-party experiments integrating AI into UI authoring.</li>
    <li><strong>Advanced Text &amp; Inputs:</strong> Multistyle text editing, full IME flags, and focus indicators.</li>
    <li><strong>Testing &amp; Inspection:</strong> Visual animation debugger and screenshot testing improvements.</li>
  </ul>
</div>

</div>

<div class="mt-6 pt-3 border-t border-zinc-800/80 flex items-center justify-between text-xs">
  <span class="text-zinc-400">Compose is the default now.</span>
  <a href="https://developer.android.com/jetpack/androidx/compose-roadmap" target="_blank" class="text-indigo-400 hover:underline font-mono no-underline flex items-center gap-1">
    <span>developer.android.com/compose-roadmap</span>
    <span>↗</span>
  </a>
</div>

<!--
Presenter Notes:
- Conclude Section 01: Connect the journey from Compose's launch to where it is today.
- Highlight the official AndroidX roadmap categories:
  1. What is solved: scroll jank parity with Views, layout animations, shadows, and strong skipping.
  2. What is in focus next: cold startup optimization, built-in scrollbars, multistyle text/IME, and GenAI UI tooling experiments.
- Direct audience to the official link to track roadmap milestones as upcoming Jetpack releases drop.
-->

---
layout: section
---

# 02 · The Android Platform

Adaptive screens, modern UX, and platform behavior

---
layout: center
class: text-center
---

<div class="text-xs font-mono uppercase tracking-widest text-rose-400 mb-3 font-semibold">
Platform Update
</div>

<h1 class="!text-4xl md:!text-5xl font-extrabold tracking-tight text-white leading-tight">
Android 17 ignores your <br>
<span class="text-transparent bg-clip-text bg-gradient-to-r from-rose-400 via-amber-300 to-orange-400 font-mono">orientation locks</span>
</h1>

<div class="mt-6 text-base text-zinc-300 max-w-xl mx-auto leading-relaxed">
Hardcoded <code>screenOrientation="portrait"</code> is ignored on screens over 600dp.
<br>
Your app <em>will</em> be resized, unfolded, and split-screened. <br>
You have to make it adaptive.
</div>

<!--
Presenter Notes:
- Deliver this with punch: For 15 years, developers "solved" tablets by sticking android:screenOrientation="portrait" in the AndroidManifest.
- Android 16 and 17 actively ignore portrait-lock and non-resizable flags on displays wider than 600dp (tablets, foldables, freeform desktop mode).
- Mention verbally: form factors are everywhere (Pixel Fold, Galaxy Fold, tablets, ChromeOS, Samsung DeX).
- You can no longer pretend foldables don't exist. Your UI must adapt to window size, which brings us to Window Size Classes.
-->

---

# Window Size Classes: The Responsive Standard

Instead of checking device models or pixel densities, Android categorizes screen width into **three Window Size Classes**:

<div class="mt-4 flex justify-center">
  <img
    src="/images/window_size_classes_width.png"
    alt="Official Android Window Width Size Classes: Compact, Medium, Expanded"
    style="max-height: 200px; width: auto; object-fit: contain;"
    class="rounded-lg shadow-xl bg-white p-1"
  />
</div>

<div class="grid grid-cols-3 gap-4 mt-4 text-xs">
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-indigo-400">📱 Compact (&lt; 600dp)</div>
    <div class="text-zinc-400 mt-1">Standard phone portrait. Single pane stack with bottom navigation bar.</div>
  </div>
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-emerald-400">📖 Medium (600–840dp)</div>
    <div class="text-zinc-400 mt-1">Unfolded foldable, small tablet. Move bottom bar to a side Navigation Rail.</div>
  </div>
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-purple-400">💻 Expanded (&gt; 840dp)</div>
    <div class="text-zinc-400 mt-1">Large tablet, desktop mode. Dual-pane List-Detail layout side by side.</div>
  </div>
</div>

<div class="text-xs opacity-50 mt-2 text-center">
Source: developer.android.com/develop/ui/compose/layouts/adaptive
</div>

<!--
Walk through the graphic.
Compact = phone portrait. Medium = foldable / 7-inch tablet. Expanded = 10-inch tablet / desktop.
Designing for these three breakpoints covers 99% of Android devices.
-->

---
layout: two-cols
---

# Adaptive Multi-Pane in Compose

Compose provides built-in scaffolds to handle multi-pane reflow effortlessly.

<div class="pr-3">

```kotlin
val navigator = rememberListDetailPaneScaffoldNavigator()

ListDetailPaneScaffold(
  directive = navigator.scaffoldDirective,
  value = navigator.scaffoldValue,
  listPane = {
    AnimatedPane { ItemList { navigator.navigateTo(Detail, it) } }
  },
  detailPane = {
    AnimatedPane { ItemDetail(navigator.currentDestination?.content) }
  }
)
```

<div class="text-[11px] opacity-75 mt-1.5 leading-tight">
Phone: navigates to full screen. <br>
Foldable/Tablet: renders dual panes side-by-side automatically.
</div>

</div>

::right::

<div class="pl-2 flex flex-col justify-center h-full pt-4">
  <AdaptiveVisual />

  <div class="mt-3 p-3 rounded-xl bg-indigo-950/30 border border-indigo-500/30 text-xs">
    <div class="flex items-center gap-1.5 font-bold text-indigo-400 text-[11px] mb-1">
      <span>💡</span>
      <span>Pro Tip: Scaffold with Android Skills</span>
    </div>
    <p class="text-zinc-300 text-[11px] leading-relaxed mb-1.5">
      Install official adaptive guidelines for AI coding agents:
    </p>
    <code class="block font-mono text-[10.5px] bg-black/50 px-2 py-1 rounded text-indigo-200 border border-indigo-500/20">
      android skills add adaptive
    </code>
  </div>
</div>

<!--
Point out that you do NOT need to write separate apps or duplicate Activities.
ListDetailPaneScaffold handles the transition and back navigation between single-pane and dual-pane automatically.
Mention the tip: You can teach coding agents modern adaptive patterns directly using the `adaptive` skill (`android skills add adaptive`).
-->

---
layout: two-cols
---

# Edge-to-Edge is Mandatory

Your app now draws **behind** the status bar and gesture navigation bar by default.

<div class="pr-4 mt-2">

<v-clicks class="text-sm">

- Android 15 and 16 made edge-to-edge rendering mandatory.
- **The failure mode:** If you forget insets, your FloatingActionButton or TopAppBar gets obscured behind system icons or the home pill.
- **The fix:** Call `enableEdgeToEdge()` in `onCreate()` and use Compose `WindowInsets` padding.

</v-clicks>

```kotlin
Scaffold(
  contentWindowInsets = WindowInsets.safeDrawing,
  topBar = { TopAppBar(/* automatically padded */) }
) { innerPadding ->
  Box(modifier = Modifier.padding(innerPadding)) {
    // Screen content safe from notches and gesture bars
  }
}
```

</div>

::right::

<div class="flex flex-col items-center justify-center h-full pl-2">

<img
  src="/images/edge-to-edge-contrast.gif"
  alt="Edge to edge status bar contrast demonstration"
  style="max-height: 330px; width: auto; object-fit: contain;"
  class="rounded-xl shadow-xl border border-zinc-800"
/>

<div class="text-[11px] opacity-60 mt-2 text-center">
Handling status bar contrast and safe drawing padding
</div>

</div>

<!--
Show the GIF.
Point out what happens when insets are ignored: text collisions with the camera cutout or navigation bar.
With Scaffold and WindowInsets.safeDrawing, it is solved cleanly.
-->

---
layout: two-cols
---

# Predictive Back Gestures

Navigation that feels physical and tactile

<div class="pr-4 mt-1">

<v-clicks class="text-xs space-y-1.5">

- **Default Path (Nav 2.8.0+):** Predictive crossfade works out-of-the-box with zero gesture math.
- **Declarative Transitions:** Customize via `popExitTransition` / `popEnterTransition` on `NavHost`.
- **Material 3 Components:** `ModalBottomSheet` & `SearchBar` animate predictive exit natively.
- **Custom Escape Hatch:** `PredictiveBackHandler` is only for manual sheets or canvas physics.

</v-clicks>

```kotlin
// Default: Nav 2.8.0+ handles it automatically
NavHost(
  navController = navController,
  startDestination = "home",
  popExitTransition = { scaleOut(targetScale = 0.9f) }
)

// Custom escape hatch (e.g. custom sheet offset)
PredictiveBackHandler { progress ->
  progress.collect { sheetOffset = it.progress }
}
```

</div>

::right::

<div class="flex flex-col items-center justify-center h-full pl-2">
  <video
    src="/videos/demo-predictive-back.mp4"
    poster="/images/predictive-back-poster.png"
    autoplay
    loop
    muted
    playsinline
    style="width: 130px; height: 290px; object-fit: cover; display: block;"
    class="rounded-xl shadow-xl border border-zinc-800"
  ></video>
  <div class="text-[11px] opacity-60 mt-2 text-center">
    User peeking at the previous screen
  </div>
</div>

<!--
Make sure the audience knows: you don't need manual gesture math for 95% of use cases.
Navigation 2.8.0+ enables predictive crossfade automatically.
Customize it declaratively with popExitTransition on your NavHost.
Material3 components like ModalBottomSheet and SearchBar also animate out of the box.
Only reach for PredictiveBackHandler when building custom sheets, drawers, or gestures.
-->

---

# Live Updates: Ongoing Activities

A dedicated notification channel for things **happening right now** in the real world.

<div class="grid grid-cols-2 gap-6 mt-4">

<div>
  <img
    src="/images/live-update-shade.png"
    alt="Food delivery order Live Update in notification shade"
    style="width: 100%; max-height: 220px; object-fit: contain;"
    class="rounded-lg shadow-xl"
  />
  <div class="text-xs opacity-70 mt-2 text-center">In the shade: Live progress bar, ETA, and actions</div>
</div>

<div>
  <img
    src="/images/live-update-chip.jpg"
    alt="Live Update collapsed into status bar chip"
    style="width: 100%; max-height: 220px; object-fit: contain;"
    class="rounded-lg shadow-xl"
  />
  <div class="text-xs opacity-70 mt-2 text-center">In the status bar: Persistent chip visible across all apps</div>
</div>

</div>

<div class="mt-4 p-3 rounded-xl bg-zinc-950 border border-zinc-800 text-xs text-zinc-300 leading-relaxed">
Shipped in Android 16 QPR / Android 17. Instead of spamming users with 10 separate notifications, an ongoing activity (cab tracking, food delivery, workout, flight status) stays updated in-place on the lock screen and status bar.
</div>

<!--
These are real screenshots from active food deliveries and rides.
Notice the persistent chip in the status bar: tap it, and it expands directly back to the app.
-->

---

# Modern Privacy: Respecting the User

Android's security model has evolved from all-or-nothing permissions to fine-grained, contextual access.

<div class="grid grid-cols-3 gap-5 mt-6 text-sm">

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-indigo-400 mb-2">📸 Photo Picker</div>
  <p class="text-zinc-300 text-xs leading-relaxed">
    No need for `READ_MEDIA_IMAGES`! The system Photo Picker lets users grant access to only the 2 photos they picked, without exposing their entire camera roll.
  </p>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-emerald-400 mb-2">🌐 Local Network Access</div>
  <p class="text-zinc-300 text-xs leading-relaxed">
    Targeting Android 17 requires explicit user permission (`ACCESS_LOCAL_NETWORK`) before discovering IoT devices, smart TVs, or casting on local Wi-Fi.
  </p>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-purple-400 mb-2">🛡️ Defensive UX</div>
  <p class="text-zinc-300 text-xs leading-relaxed">
    Users can deny any permission or revoke it in settings. <strong>Never assume permission is granted.</strong> Always design a graceful fallback flow.
  </p>
</div>

</div>

<!--
Teach the students good engineering hygiene: never write code that crashes if a permission is denied.
Use modern system pickers wherever possible so you don't even need to ask for permissions in the manifest.
-->

---
layout: center
---

# Platform Takeaway

The Android platform has clear design and behavioral guidelines.

<div class="mt-4 opacity-80 text-base max-w-xl mx-auto leading-relaxed">
Stop assuming a fixed portrait rectangle. Assume your app will be resized, rotated, and put next to other windows.
<br><br>
Build responsive layouts with Window Size Classes, draw cleanly edge-to-edge, and design assuming permissions can be denied.
</div>

---
layout: section
---

# 03 · Kotlin Multiplatform

One language, native execution on every target

---

# The Cross-Platform Problem

Picture a typical product team building an Android and an iOS app:

<v-clicks class="text-sm">

- **The Old Reality:** You write the networking layer, JSON serialization, SQLite caching, and validation rules in Kotlin for Android.
- Then another developer (or you, wearing a second hat) writes the **exact same logic** in Swift for iOS.
- **The Pain:** Two codebases to maintain, bugs fixed on Android that remain broken on iOS, and subtle behavioral drift between platforms.

</v-clicks>

<div v-click class="mt-6 p-4 rounded-xl bg-zinc-950 border border-zinc-800 text-sm">
  <div class="font-bold text-indigo-400 mb-1">Enter Kotlin Multiplatform (KMP):</div>
  <div class="text-zinc-300 leading-relaxed">
    What if you could write your shared logic <strong>once in Kotlin</strong>, compile it directly to a native iOS framework, and keep 100% native UI on both sides?
  </div>
</div>

<!--
Frame KMP through real product pain.
Every startup and team hates writing API models and business calculations twice.
-->

---

# The 3 Levels of KMP Adoption

You don't have to rewrite your entire app on day one. JetBrains designed KMP for **incremental adoption**:

<div class="mt-4 flex justify-center">
  <img
    src="/images/kmp/kmp-graphic.png"
    alt="Three levels of Kotlin Multiplatform adoption"
    style="max-height: 250px; width: auto; object-fit: contain;"
    class="rounded-lg shadow-xl"
  />
</div>

<div class="grid grid-cols-3 gap-4 mt-4 text-xs">
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-indigo-400">Level 1: Share a Piece of Logic</div>
    <div class="text-zinc-400 mt-1">Share complex validation, pricing algorithms, or encryption helpers in a single shared file.</div>
  </div>
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-emerald-400">Level 2: Share Data &amp; Logic</div>
    <div class="text-zinc-400 mt-1">Share Ktor networking, Room database, and ViewModels. Keep native Compose on Android &amp; SwiftUI on iOS.</div>
  </div>
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-purple-400">Level 3: Share the UI (CMP)</div>
    <div class="text-zinc-400 mt-1">Use Compose Multiplatform to share screens across Android, iOS, desktop, and web.</div>
  </div>
</div>

<!--
This is the most comforting slide for students and devs.
You do not have to commit to 100% cross-platform. You can start with a single shared helper module.
-->

---
layout: two-cols
---

# How It Works Under the Hood

Kotlin isn't just one compiler—it has **multiple native backends**:

<div class="pr-4 mt-2">

<v-clicks class="text-sm">

- **Kotlin/JVM:** Compiles to JVM bytecode for Android & backend microservices.
- **Kotlin/Native:** Compiles directly to machine code via **LLVM** for iOS, macOS, Windows, and Linux.
- **Kotlin/Wasm:** Compiles to WebAssembly for high-performance web canvas execution.

</v-clicks>

<div v-click class="mt-4 p-3 rounded-lg bg-indigo-950/40 border border-indigo-800/40 text-xs text-indigo-200">
<strong>The crucial distinction:</strong> To Xcode, your shared Kotlin code is compiled into an ordinary <code>.framework</code>. The iOS app calls it just like any native Swift dependency—no JavaScript bridge and no VM overhead!
</div>

</div>

::right::

<div class="flex flex-col items-center justify-center h-full pl-2">

<img
  src="/images/kmp/multiplatform-executables-diagram.svg"
  alt="Kotlin multiplatform compilation diagram"
  style="max-height: 290px; width: auto; object-fit: contain;"
  class="rounded-xl shadow-xl bg-white p-2"
/>

<div class="text-[11px] opacity-60 mt-2 text-center">
Source: kotlinlang.org — Multiplatform project architecture
</div>

</div>

<!--
Contrast this with Flutter and React Native.
React Native ships a JavaScript engine. Flutter ships a complete C++ engine.
KMP compiles down to native CPU instructions via LLVM for iOS.
-->

---
layout: two-cols
---

# Platform APIs: `expect` / `actual`

When shared code needs device capabilities (battery, camera, hardware info), Kotlin enforces compile-time contracts:

<div class="pr-4 mt-2">

**`commonMain` (Interface contract)**

```kotlin
// Declares the shape, no body
expect fun getDeviceModel(): String
```

<div class="text-xs opacity-75 mt-3 leading-relaxed">
Missing an <code>actual</code> implementation for any target? <strong>The compiler fails the build</strong> before you ever ship to production.
</div>

</div>

::right::

<div class="pl-2 mt-2">

**`androidMain`**

```kotlin
actual fun getDeviceModel(): String =
  "${Build.MANUFACTURER} ${Build.MODEL}"
```

**`iosMain`**

```kotlin
actual fun getDeviceModel(): String =
  UIDevice.currentDevice.model
```

</div>

<!--
The compiler enforces that every platform supplies an implementation.
JetBrains advice: use dependency injection and interfaces for business logic,
and keep expect/actual for genuine platform-specific hardware or OS calls.
-->

---
layout: two-cols
---

# Level 1: Share a Piece of Logic

The smallest useful starting point: one function, no UI changes, zero risk.

<div class="pr-4 mt-1">

```kotlin
// commonMain (Shared Kotlin)
fun isValidUpiId(input: String): Boolean {
  val parts = input.split("@")
  return parts.size == 2 && 
         parts.all { it.isNotBlank() }
}
```

<div class="text-xs opacity-75 mt-3 leading-relaxed">
Validation rules, pricing math, date formatting, and crypto helpers — code where <strong>the two platforms silently disagreeing is a real bug</strong>.
</div>

</div>

::right::

<div class="pl-2 mt-1">

**Android calls it as Kotlin**

```kotlin
if (isValidUpiId(text)) {
  submit()
}
```

**iOS calls it natively as Swift**

```swift
// Swift calls into the compiled framework
if ValidationKt.isValidUpiId(input: text) {
  submit()
}
```

</div>

<!--
Emphasize how low the barrier to entry is.
You don't have to rewrite your whole app to get value from KMP.
A single shared validation function gives you cross-platform consistency on day one.
-->

---
layout: two-cols
---

# "I could've just copy-pasted that"

Fair — for a five-line validator. The real ROI arrives once your shared code has **dependencies**.

<div class="pr-4 mt-1">

```kotlin
// commonMain (Shared Kotlin)
@Serializable
data class ExchangeRate(val code: String, val rate: Double)

class RatesRepository(private val client: HttpClient) {
  suspend fun fetchRates(): List<ExchangeRate> =
    client.get("https://api.example.com/rates").body()
}
```

<div class="text-xs opacity-75 mt-3 leading-relaxed">
<strong>Ktor</strong> and <strong>kotlinx.serialization</strong> are official multiplatform libraries. You don't need Retrofit on Android and Alamofire on iOS.
</div>

</div>

::right::

<div class="pl-2 mt-1">

<v-clicks class="text-sm space-y-2">

- **Zero API drift:** Both apps serialize identical network payloads, SSL pinning, and error models.
- **Official Google KMP Libraries:** Google ships **Room**, **DataStore**, **ViewModel**, and **Lifecycle** as multiplatform artifacts.
- **Coroutines & Flow:** Reactive concurrency logic written and unit-tested once.

</v-clicks>

<div v-click class="mt-4 p-3 rounded-lg bg-indigo-950/40 border border-indigo-800/40 text-xs text-indigo-200">
You aren't sharing a trivial helper function — <strong>you are eliminating the entire parallel networking and persistence layer.</strong>
</div>

</div>

<!--
The counter to "I'll just write it twice" is dependencies, not lines of code.
A developer can copy a validator function in 10 seconds.
They cannot copy Room DB migrations, Ktor SSL pinning, or cache invalidation logic without continuous sync bugs.
-->

---
layout: two-cols
---

# Level 2: Shared Logic, Native UI

Write your data layer once, render with Compose on Android and SwiftUI on iOS.

<div class="pr-4 mt-1">

```kotlin
// commonMain (Shared Kotlin)
class WeatherRepository(private val api: KtorClient) {
  suspend fun getForecast(city: String): Forecast =
    api.fetch(city)
}

class WeatherViewModel(
  private val repo: WeatherRepository
) : ViewModel() {
  val state = MutableStateFlow<WeatherUiState>(Loading)
  fun refresh(city: String) { /* coroutine */ }
}
```

<div class="text-xs opacity-75 mt-2">
Ktor (HTTP), kotlinx.serialization (JSON), and Room (Database) are all official multiplatform libraries!
</div>

</div>

::right::

<div class="pl-2 mt-1">

**Android — Jetpack Compose**

```kotlin
val uiState by viewModel.state.collectAsState()
when (val state = uiState) {
  is Success -> WeatherCard(state.temp)
}
```

**iOS — SwiftUI**

```swift
// Consumed natively in Swift!
@ObservedObject var vm: WeatherViewModel
var body: some View {
  if let data = vm.state.success {
    WeatherView(temp: data.temp)
  }
}
```

</div>

<!--
This is where the real commercial ROI is for companies.
The data layer, business rules, caching, and network models are written and unit-tested once.
The UI can still be 100% native if your team prefers SwiftUI on iOS.
-->

---
layout: two-cols
---

# Level 3: Compose Multiplatform

When you want to share the user interface too

<div class="pr-4 mt-1">

<v-clicks class="text-sm">

- **Compose Multiplatform (CMP)** brings Google's Jetpack Compose to iOS, desktop, and web.
- On iOS, it renders directly onto a **Metal layer using the Skia graphics engine**.
- **Shared Codebase:** You write your `@Composable` screens in `commonMain`, and they run on Android and iOS simultaneously.
- **Two-Way Interop:** You can embed native UIKit / SwiftUI views inside Compose, or embed a Compose screen inside an iOS app.

</v-clicks>

<div v-click class="mt-3 text-xs opacity-80">
Shipped in production by: <strong>Physics Wallah (17M users), Cash App, McDonald's, Duolingo, and Forbes.</strong>
</div>

</div>

::right::

<div class="pl-2">

```kotlin
// commonMain — Runs on Android & iOS!
@Composable
fun UserProfileScreen(user: User) {
  Column(
    modifier = Modifier.fillMaxSize().padding(16.dp),
    horizontalAlignment = Alignment.CenterHorizontally,
  ) {
    AsyncImage(
      model = user.avatarUrl,
      contentDescription = "Avatar",
      modifier = Modifier.size(96.dp).clip(CircleShape)
    )
    Text(
      text = user.name,
      style = MaterialTheme.typography.titleLarge
    )
    Button(onClick = { /* shared logic */ }) {
      Text("Send Message")
    }
  }
}
```

</div>

<!--
Tie this back to Section 01: every single thing students learn about Jetpack Compose transfers directly to iOS, Desktop, and Web with CMP!
-->

---

# Where CMP Runs, and How Far to Trust It

Compose Multiplatform stability matrix and platform readiness:

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

| Target | Renders Through | Production Status |
|---|---|---|
| **Android** | Jetpack Compose (direct) | **Stable** |
| **iOS** | Skia → Metal (UIViewController) | **Stable** (1.8+) |
| **Desktop** | Skia on JVM (macOS/Win/Linux) | **Stable** |
| **Web** | Kotlin/Wasm → HTML Canvas | **Beta** |

<div class="text-xs opacity-70 mt-3 leading-relaxed">
iOS reached <strong>Stable</strong> in 1.8 (May 2025). That was the watershed moment turning CMP from an experimental demo into production-ready software.
</div>

</div>

<div>

**Recent Capabilities Landed:**

<v-clicks class="text-sm space-y-2 mt-1">

- **Shared Navigation 3:** Type-safe backstack running across Android, iOS, and Desktop.
- **1.11 Concurrent Rendering:** Metal rendering pipeline overhauled for buttery 120Hz ProMotion on iPhones.
- **1.12 Native iOS Text Input:** Real iOS selection handles, native magnifier, system copy/paste menu, and password autofill.

</v-clicks>

</div>

</div>

<!--
Be honest about status: iOS is stable and shipping to tens of millions of users.
Web is Beta because Kotlin/Wasm is still maturing.
Text editing was the last hurdle on iOS, and JetBrains resolved it with native text field delegates in 1.11/1.12.
-->

---
layout: two-cols
---

# The Code You Know, with Two Seams

Sharing UI across platforms only introduces two minor differences from regular Android Compose:

<div class="pr-4 mt-1">

**`commonMain` — The Screen**

```kotlin
@Composable
fun App() = MaterialTheme {
  Column(Modifier.fillMaxSize()) {
    // Seam 1: Res instead of R
    Image(painterResource(Res.drawable.hero), null)
    Text(stringResource(Res.string.welcome))
  }
}
```

<div class="text-xs opacity-75 mt-2">
<strong>Seam 1 — Resources:</strong> Instead of Android-specific <code>R.string</code>, CMP auto-generates a multiplatform <code>Res</code> accessor from <code>composeResources/</code>.
</div>

</div>

::right::

<div class="pl-2 mt-1">

**Seam 2 — Platform Entry Points**

```kotlin
// androidMain
setContent { App() }
```

```kotlin
// iosMain — standard UIViewController
fun MainViewController() =
  ComposeUIViewController { App() }
```

<div class="text-xs opacity-75 mt-3 leading-relaxed">
<strong>Two-Way Interop:</strong> Because CMP compiles to a real <code>UIViewController</code>, SwiftUI can embed a Compose screen — and Compose can embed native iOS views via <code>UIKitView { MKMapView() }</code>.
</div>

</div>

<!--
This is the answer to "what if Compose can't do something on iOS?"
You aren't trapped in a sandbox. You can drop down to a native UIKit or SwiftUI view whenever you need camera, maps, or Apple Pay.
-->

---
layout: two-cols
---

# "But isn't cross-platform slow?"

How Compose Multiplatform achieves native 60–120 FPS on iOS devices:

<div class="pr-4 mt-2">

<v-clicks class="text-sm space-y-3">

- **Direct Metal Rendering:** Compose on iOS does **not** generate UIKit views and does not use a JavaScript bridge.
- It renders directly via **Skia hardware-accelerated on Apple's Metal API**.
- Skips UIKit layout passes and view hierarchies, avoiding Auto Layout bottleneck penalties on heavy lists.
- Frame rates on iPhone 13 through 16 overlap within margin of error compared to native SwiftUI.

</v-clicks>

</div>

::right::

<div class="flex flex-col items-center justify-center h-full pl-2">

<img
  src="/images/kmp/cmp-ios-performance.png"
  alt="Benchmark showing Compose Multiplatform vs SwiftUI scrolling performance on iOS"
  style="max-height: 250px; width: auto; object-fit: contain;"
  class="rounded-lg shadow-xl"
/>

<div class="text-[10px] opacity-60 mt-2 text-center">
Source: JetBrains benchmarks — SwiftUI vs Compose Multiplatform FPS
</div>

</div>

<!--
Address the elephant in the room: developers remember Cordova, early React Native, or sluggish webviews.
Explain Skia -> Metal. Compose draws pixels directly to the screen like a modern game engine.
-->

---

# It's Not a Demo Anymore

Major consumer and enterprise applications built on Compose Multiplatform:

<div class="grid grid-cols-3 gap-4 mt-6 text-xs">

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-indigo-400 text-sm mb-1">Physics Wallah</div>
  <div class="text-zinc-300 font-semibold mb-1">17 Million+ Active Users</div>
  <p class="text-zinc-400 leading-relaxed">
    Complete ed-tech ecosystem with streaming, quizzes, and courseware sharing 80%+ UI and business logic across iOS and Android.
  </p>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-emerald-400 text-sm mb-1">Markaz</div>
  <div class="text-zinc-300 font-semibold mb-1">5 Million+ Downloads</div>
  <p class="text-zinc-400 leading-relaxed">
    E-commerce social marketplace with 100+ production screens written 100% in Compose Multiplatform.
  </p>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-purple-400 text-sm mb-1">Wrike & Bilibili</div>
  <div class="text-zinc-300 font-semibold mb-1">Enterprise & Social Scale</div>
  <p class="text-zinc-400 leading-relaxed">
    Complex enterprise dashboards, Gantt charts, and high-concurrency instant messaging modules shipped cross-platform.
  </p>
</div>

</div>

<div v-click class="mt-6 p-3 rounded-lg bg-zinc-950 border border-zinc-800 text-xs text-zinc-300 text-center">
<strong>Pattern Notice:</strong> Content, forms, dashboards, and media-rich transactional apps benefit most. You get 90% code reuse without sacrificing native feel.
</div>

<!--
These real examples establish credibility.
Physics Wallah is particularly relatable in India — millions of students use it daily on budget Android phones and premium iPhones alike.
-->

---
layout: center
---

# KMP Takeaway

Kotlin is no longer just "the Android language."

<div class="mt-4 opacity-80 text-base max-w-xl mx-auto leading-relaxed">
KMP allows you to share what makes sense (networking, databases, viewmodels) while preserving complete native access to platform APIs.
<br><br>
Start small: share a validation helper or a Ktor API client for your next team project.
</div>

---
layout: center
class: text-center
---

# The Elephant in the Room

<div class="mt-4 flex flex-col items-center justify-center">
  <img
    src="/images/ai/the-address-me-elephant-in-the-room-v0-qei5c408f8of1.webp"
    alt="The Elephant in the Room meme"
    style="max-height: 380px; width: auto; object-fit: contain;"
    class="rounded-xl shadow-2xl border border-zinc-800"
  />
  <div class="mt-3 text-xs opacity-60">"We need to talk about AI in Android..."</div>
</div>

<!--
Humorous transition into the AI section:
Acknowledge the elephant in the room. Everyone is talking about AI, but how does it actually fit into Android?
We are going to look at it from two concrete angles:
1. On-Device AI inside your apps
2. How you use AI tools as an Android Engineer to build faster and smarter.
-->

---
layout: section
---

# 04 · AI in Android Development

From on-device models to agentic developer tooling

---
layout: section
---

# Part A · On-Device AI

AICore, Gemini Nano, and local intelligence

---

# Why On-Device AI?

Everyone wants to add intelligence to their apps, but cloud-only models carry real trade-offs:

<div class="grid grid-cols-2 gap-6 mt-6 text-sm">

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-rose-400 text-base mb-2">☁️ Cloud LLMs (Cloud API)</div>
  <ul class="text-zinc-300 text-xs space-y-2">
    <li>• <strong>Latency:</strong> 1 to 3 seconds per round trip over network.</li>
    <li>• <strong>Cost:</strong> Continuous recurring API bills per token.</li>
    <li>• <strong>Privacy:</strong> User data must leave the device and hit servers.</li>
    <li>• <strong>Offline:</strong> Completely fails in airplane mode or spotty signal.</li>
  </ul>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-indigo-500/40 bg-indigo-950/20">
  <div class="font-bold text-emerald-400 text-base mb-2">📱 On-Device Models (Gemini Nano)</div>
  <ul class="text-zinc-300 text-xs space-y-2">
    <li>• <strong>Instant:</strong> Local inference on NPU/GPU with sub-second response.</li>
    <li>• <strong>Free:</strong> Zero cloud server costs per user query.</li>
    <li>• <strong>Private:</strong> Sensitive personal data never leaves the hardware.</li>
    <li>• <strong>Offline:</strong> Works 100% offline in airplane mode.</li>
  </ul>
</div>

</div>

<!--
Frame this clearly for college students:
Cloud models are great for huge knowledge tasks.
On-device models are ideal for personal, local tasks: summarizing a private note, proofreading a text, smart replies, or transcribing voice.
-->

---

# Two Directions of On-Device AI

Most developers think "AI on Android" just means asking a model for text. In modern Android, it is **two opposite directions**:

<div class="mt-4 flex justify-center">
  <img
    src="/images/ai/two-directions.svg"
    alt="Two directions of on-device AI: App calling ML Kit/LiteRT, and Gemini assistant calling AppFunctions"
    style="max-height: 270px; width: auto; object-fit: contain;"
    class="rounded-lg shadow-xl"
  />
</div>

<div class="grid grid-cols-2 gap-6 mt-4 text-xs">
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-indigo-400">Direction 1: App Calls Model</div>
    <div class="text-zinc-400 mt-1">Your app passes text or images to local Gemini Nano to summarize, proofread, or categorize.</div>
  </div>
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-purple-400">Direction 2: System Calls App (AppFunctions)</div>
    <div class="text-zinc-400 mt-1">Your app registers callable tools. The system Gemini assistant invokes your app to perform user actions!</div>
  </div>
</div>

<!--
This is the core insight of the section.
Keep the two arrows clear:
1. You calling the model.
2. The AI assistant calling your app as an agent tool.
-->

---
layout: two-cols
---

# The Shared System Model: Android AICore

Bundling a 2GB model inside every APK would destroy phone storage. **AICore** solves this.

<div class="pr-4 mt-2">

<v-clicks class="text-sm">

- **System-Level Service:** Gemini Nano is managed by the Android operating system, not bundled inside your APK.
- **One Shared Copy:** Every app on the device shares the same foundation model instance. Your APK stays compact.
- **Private Compute Core:** Runs in an isolated sandbox with zero direct internet access.
- **Background Updates:** Google updates model weights and hardware NPU optimizations through system updates.

</v-clicks>

</div>

::right::

<div class="pl-2 mt-2">

```kotlin
// The entire developer API is availability checking!
val client = Generation.getClient()

when (client.checkStatus()) {
  FeatureStatus.AVAILABLE -> {
    // Ready for instant offline inference
    val summary = client.generateContent(
      "Summarize in 10 words: $text"
    )
  }
  FeatureStatus.DOWNLOADABLE -> {
    // Show download progress in UI
    client.download().collect { progress -> ... }
  }
  FeatureStatus.UNAVAILABLE -> {
    // Fall back to cloud or hide feature
  }
}
```

</div>

<!--
Emphasize the defensive programming aspect:
Notice that 80% of the code is handling status checks!
On-device AI requires handling cases where the model is still downloading or unsupported.
-->

---
layout: two-cols
---

# AppFunctions: Your App as an AI Tool

The other arrow: making your app callable by system assistants (Gemini)

<div class="pr-4 mt-1">

<v-clicks class="text-sm">

- **On-Device Agent Tools:** In Android 16+, apps can register `AppFunction` endpoints.
- When a user asks Gemini: *"Book a cab to the airport"* or *"Create a task to buy groceries"*, Gemini identifies the right app tool and executes it locally.
- **KDoc is the Prompt:** Notice how KDoc comments provide the schema and description that the model uses to understand your function!

</v-clicks>

</div>

::right::

<div class="pl-2 mt-1">

```kotlin
@AppFunctionSerializable(isDescribedByKDoc = true)
data class CreateTaskParams(
  /** Title of the reminder task. */
  val title: String,
  /** Due date formatted as ISO-8601. */
  val dueDate: String?
)

@AppFunctionServiceEntryPoint
abstract class TaskFunctions : AppFunctionService() {

  /**
   * Creates a new todo item in the user database.
   * @param params Task parameters.
   */
  @AppFunction(isDescribedByKDoc = true)
  suspend fun createTask(params: CreateTaskParams): TaskResult =
    withContext(Dispatchers.IO) {
      repository.insert(params.title, params.dueDate)
    }
}
```

</div>

<!--
Students find this fascinating: documentation comments are no longer just for developers—they are parsed by AI models at runtime to determine function arguments!
-->

---
layout: two-cols
---

# When You Need Your Own Model: LiteRT-LM

Gemini Nano is chosen for you by Google. **LiteRT-LM** is the runtime when you need to bring your *own* model.

<div class="pr-4 mt-2">

```kotlin
val engine = Engine(
  EngineConfig(
    modelPath = "/data/.../gemma-2b.litertlm",
    backend = Backend.GPU(),
  )
)
engine.initialize()

engine.createConversation().use { chat ->
  chat.sendMessageAsync("Analyze transaction")
    .collect { token -> print(token) }
}
```

</div>

::right::

<div class="pl-2 mt-2">

<v-clicks class="text-sm space-y-2">

- **You own the model file:** Runs on any Android device meeting hardware requirements — no AICore or Pixel allowlist needed.
- **Hardware Acceleration:** Native NPU and GPU acceleration via Qualcomm, MediaTek, and Tensor delegates.
- **The Catch — Download Size:** Gemma 2B is **~2.6 GB**. That is a serious storage discussion with your user, not a typical Gradle dependency.
- **LiteRT runtime:** The same engine powering on-device AI across Chrome, ChromeOS, and Pixel Watch.

</v-clicks>

</div>

<!--
Students and engineers often ask: "Can I run Llama 3 or my own fine-tuned model?"
Yes, through LiteRT-LM. But emphasize the storage cost — 2.6 GB is a huge barrier for mobile users.
Note for audience: Mention verbally that all of this is currently beta, with AppFunctions being alpha.
-->

---
layout: center
---

# On-Device AI Takeaway

AI is becoming a standard Android platform API.

<div class="mt-4 opacity-80 text-base max-w-xl mx-auto leading-relaxed">
The skill isn't prompt engineering—it's <strong>defensive engineering</strong>: checking device capability, budgeting for download progress, and designing seamless fallbacks.
<br><br>
Soon, an app's job isn't just to display a UI for humans, but to be a reliable tool for intelligent assistants.
</div>

---
layout: section
---

# Part B · Using AI as an Android Engineer

Agent-first workflows, CLI tooling, Skills, and autonomous debugging

---

# The Shift: From Chatbots to Autonomous Agents

Copilots in the IDE are handy for autocompletion, but modern engineering workflows are moving to **autonomous agentic loops**:

<div class="grid grid-cols-2 gap-6 mt-6 text-sm">

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-rose-400 text-base mb-2">💬 Traditional Chat Assistant</div>
  <ul class="text-zinc-300 text-xs space-y-2">
    <li>• You copy-paste errors or code snippets back and forth.</li>
    <li>• LLM is <strong>blind</strong> to runtime emulator state and crashes.</li>
    <li>• Relies on outdated generic training data (suggests deprecated APIs).</li>
    <li>• Human developer does all manual compilation and verification.</li>
  </ul>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-indigo-500/40 bg-indigo-950/20">
  <div class="font-bold text-emerald-400 text-base mb-2">🤖 Autonomous Agent Workflow</div>
  <ul class="text-zinc-300 text-xs space-y-2">
    <li>• Agent directly executes build commands and inspects emulator output.</li>
    <li>• Guided by <strong>Skills</strong> containing official, modern Android patterns.</li>
    <li>• Uses headless CLI tools to debug crashes and inspect UI trees.</li>
    <li>• <strong>Closed-loop verification:</strong> Writes code &rarr; builds &rarr; runs &rarr; fixes errors autonomously.</li>
  </ul>
</div>

</div>

<!--
Pivot cleanly from on-device AI in the app to AI developer tooling.
Explain the paradigm shift: we are moving past copy-pasting code into ChatGPT.
Modern Android engineers use agentic tools that actually interface with the Android toolchain.
-->

---
layout: two-cols
---

# Google's Android CLI & Knowledge Base

Google introduced a dedicated terminal-first, agent-friendly toolset for Android:

<div class="pr-4 mt-2">

<v-clicks class="text-sm space-y-3">

- **Machine-Readable Interfaces:** Instead of parsing noisy Gradle logs or UI screens, `android-cli` outputs structured JSON.
- **70% Token Savings & 3x Faster:** Agents don't burn context windows reading gigabytes of console spew.
- **Headless Operations:** Scaffolding projects, managing SDKs, launching emulators, and running instrumented tests directly from the shell.
- **Real-Time Knowledge Base:** Live, version-accurate documentation grounding for Gemini, Claude Code, and AGY.

</v-clicks>

</div>

::right::

<div class="pl-2 mt-2">

```bash
# Initialize project with agent tooling
android init

# Agent queries project structure & dependencies
android project inspect --format=json

# Launch & capture structured UI hierarchy
android emulator capture-layout --output=ui.json

# Run targeted checks with machine output
android test run --target=:app:testDebugUnitTest \
  --output-format=json
```

<div class="mt-3 p-3 rounded-lg bg-indigo-950/30 border border-indigo-800/40 text-xs text-indigo-300">
  <strong>Why it matters:</strong> Bridges the gap between LLM reasoning and the Android SDK toolchain.
</div>

</div>

<!--
Explain android-cli:
It was built specifically because AI coding agents struggle with huge human-readable log dumps.
By providing structured JSON outputs and CLI hooks, agents can inspect apps efficiently.
-->

---
layout: two-cols
---

# Grounding Agents: Android Skills

LLMs hallucinate deprecated APIs (`findViewById`, old XML navigation) because 15 years of legacy code dominates their pretraining data.

<div class="pr-4 mt-2">

### What is a Skill?

<v-clicks class="text-sm space-y-2">

- **Modular Knowledge Bundles:** Specialized markdown instruction specs (`SKILL.md`) installed in `.agents/skills/`.
- **Automatic Triggering:** Agents detect relevant tasks (e.g., *"Migrate this screen to Navigation 3"* or *"Implement predictive back"*).
- **Enforces 2026 Standards:** Ensures the agent writes strict Modern Android Architecture, avoiding deprecated libraries.

</v-clicks>

</div>

::right::

<div class="pl-2 mt-2">

```markdown
<!-- .agents/skills/android-compose/SKILL.md -->
---
name: android-compose
description: Rules and patterns for Jetpack Compose
---

# Jetpack Compose Rules
1. Never mutate state inside composables.
2. Use `rememberSaveable` for UI survival across recreate.
3. Use Navigation 3 collection-based backstacks.
4. Always handle WindowInsets with innerPadding.
```

<div class="mt-4 p-3 rounded-xl bg-zinc-950 border border-zinc-800 text-xs text-zinc-300">
  💡 <strong>Skill + Tooling:</strong> The agent activates the skill, generates modern Compose code, and tests it with <code>android-cli</code>.
</div>

</div>

<!--
Explain how Skills solve the hallucination problem for Android.
Since Android has changed so drastically, without Skills an LLM defaults to 2018 StackOverflow answers.
Skills keep the AI aligned with modern best practices.
-->

---
layout: two-cols
---

# Autonomous Runtime Debugging: Debroid

AI agents could write code and read logs, but were historically blind to runtime execution state. **[debroid](https://github.com/PatilShreyas/debroid)** changes that.

<div class="pr-4 mt-2">

<v-clicks class="text-sm space-y-2">

- **Created by [Shreyas Patil](https://github.com/PatilShreyas):** Android GDE & open-source developer.
- **Headless Android Debugger:** Operates over JDWP (Java Debug Wire Protocol) completely without Android Studio GUI.
- **Agent-Ready JSON Output:** Emits machine-readable debugging data tailored for LLMs.
- **Full Debugging Loop:** AI agents can set breakpoints, catch unhandled exceptions, step through code, and inspect/mutate variable state in a running APK.

</v-clicks>

</div>

::right::

<div class="pl-2 mt-2">

```bash
# Start debroid session on target package
debroid attach --package com.example.app --port 8700

# Set breakpoint on ViewModel method
debroid breakpoint set \
  --class com.example.app.UserViewModel \
  --line 42 --json

# Agent inspects local variables at runtime
debroid state inspect --frame 0 --json
# Output:
# {"status":"paused","vars":{"userId":"42","state":"Loading"}}

# Resume execution
debroid resume
```

<div class="mt-3 text-xs opacity-75">
  <a href="https://github.com/PatilShreyas/debroid" target="_blank" class="text-indigo-400 underline">github.com/PatilShreyas/debroid</a> — Autonomous debugging for AI agents
</div>

</div>

<!--
Highlight Shreyas Patil's debroid:
Explain how breakthrough this is: until debroid, if an app crashed with a cryptic NullPointerException or runtime race condition, the agent had to guess from stack traces.
With debroid, the agent attaches to the JVM process via JDWP, inspects variables at breakpoints, and fixes the bug accurately.
-->

---
layout: center
---

# The Future: The Full AI Engineering Loop

How modern Android engineers supercharge their velocity:

<div class="grid grid-cols-3 gap-4 mt-6 text-sm">

<div class="p-4 rounded-xl bg-zinc-950 border border-indigo-500/40 bg-indigo-950/10">
  <div class="font-bold text-indigo-400 mb-2">1. Grounding (Skills)</div>
  <div class="text-xs text-zinc-300 leading-relaxed">
    Agent consults official Android Skills and live knowledge bases to generate modern Jetpack Compose and KMP architecture.
  </div>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-purple-500/40 bg-purple-950/10">
  <div class="font-bold text-purple-400 mb-2">2. Tooling (android-cli)</div>
  <div class="text-xs text-zinc-300 leading-relaxed">
    Agent runs builds, orchestrates emulators, and parses structured test results with zero token bloat.
  </div>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-emerald-500/40 bg-emerald-950/10">
  <div class="font-bold text-emerald-400 mb-2">3. Debugging (debroid)</div>
  <div class="text-xs text-zinc-300 leading-relaxed">
    If a runtime crash occurs, the agent attaches headlessly via JDWP, inspects variables, and patches the bug autonomously.
  </div>
</div>

</div>

<div class="mt-6 text-xs text-center text-zinc-400">
  You remain the architect making the product and design decisions; AI agents handle the repetitive plumbing and debugging.
</div>

<!--
Summarize Part B:
The engineer's role evolves to architect, system designer, and code reviewer.
Skills ground the agent, android-cli operates the build/device, and debroid debugs runtime issues.
-->

---
layout: two-cols
---

# Practical Prompts: Root Cause vs Symptoms

How you formulate the prompt determines whether the agent hacks a workaround or actually fixes the bug.

<div class="pr-4 mt-2">

<div class="text-xs text-zinc-300 leading-relaxed mb-4">
I was recently reviewing a pull request and noticed that in Spanish translations, the text was off vertical center.
</div>

<div class="p-3 rounded-xl bg-rose-950/30 border border-rose-800/40 mb-3 text-xs">
  <div class="font-bold text-rose-400 mb-1">❌ Asking for the symptom:</div>
  <div class="font-mono text-zinc-300">"Fix the text not being vertically aligned"</div>
  <div class="text-zinc-400 mt-1.5 text-[11px]">
    The agent might add hardcoded paddings, manual offsets (<code>offset(y = -8.dp)</code>), or quick hacks that break in other locales and screen sizes!
  </div>
</div>

<div class="p-3 rounded-xl bg-emerald-950/30 border border-emerald-800/40 text-xs">
  <div class="font-bold text-emerald-400 mb-1">✅ Asking to investigate the cause:</div>
  <div class="font-mono text-zinc-200">"Pull up the current layout and fix the issue causing the vertical misalignment of the title text"</div>
  <div class="text-zinc-300 mt-1.5 text-[11px]">
    Directs the agent to inspect the layout structure, font metrics, line heights, or constraint chains rather than patching the symptom.
  </div>
</div>

</div>

::right::

<div class="flex flex-col items-center justify-center h-full pl-4">

<img
  src="/images/ai/647967014-7b3a96af-3b50-4d8b-8d11-691ac109129d.png"
  alt="Spanish translation UI showing vertical text misalignment"
  style="max-height: 420px; width: auto; object-fit: contain;"
  class="rounded-xl shadow-2xl border border-zinc-800"
/>

<div class="mt-2 text-[11px] opacity-60 text-center">
  Real PR review: Multi-line Spanish title pushing vertical alignment
</div>

</div>

<!--
Personal, highly practical story:
Reviewing a PR where localization broke vertical centering.
Explain prompt precision:
If you tell an agent "make the text vertically aligned", it might add a hacky padding or hardcoded offset.
If you instruct it to "pull up the current layout and fix the issue causing the vertical misalignment", it checks the hierarchy, wraps, and baseline alignment properly.
-->

---
layout: center
---

# 3 Rules for Prompting Coding Agents

Concrete principles for getting clean, senior-engineer-grade code from AI agents:

<div class="grid grid-cols-3 gap-5 mt-6 text-sm">

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-indigo-400 mb-2">1. Point to the Root Cause</div>
  <div class="text-xs text-zinc-300 leading-relaxed">
    Don't prescribe UI bandaids (e.g. <em>"add 8dp padding"</em>). Ask the agent to inspect the container layout and resolve the fundamental structural constraint.
  </div>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-purple-400 mb-2">2. Anchor to Existing Patterns</div>
  <div class="text-xs text-zinc-300 leading-relaxed">
    Prompt with codebase references: <em>"Follow the MVI pattern used in FeatureX"</em> or <em>"Use our design system's AppButton"</em> so it doesn't reinvent the wheel.
  </div>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-emerald-400 mb-2">3. Require Verification Steps</div>
  <div class="text-xs text-zinc-300 leading-relaxed">
    Instruct the agent to verify: <em>"Build the debug variant and run unit tests to confirm no regression"</em> or check with <code>android-cli</code>.
  </div>
</div>

</div>

<div class="mt-6 text-xs text-center text-zinc-400">
  Clear intent + structural investigation + verification = high-quality, maintainable code.
</div>

<!--
Wrap up prompting advice:
Senior engineers don't write vague prompts. They treat the agent like a junior pair programmer: clear context, right file pointers, and requirement to verify.
-->

---
layout: section
---

# 05 · Putting It All Together

Modern Android Architecture in production

---

# How the Pieces Connect: Architecture

Here is how Google's official Modern Android Architecture (MAD) connects the entire stack:

<div class="mt-4 flex justify-center">
  <img
    src="/images/mad-arch-overview.png"
    alt="Official Modern Android Architecture Overview: UI Layer, Domain Layer, Data Layer"
    style="max-height: 250px; width: auto; object-fit: contain;"
    class="rounded-lg shadow-2xl bg-white p-2"
  />
</div>

<div class="grid grid-cols-3 gap-4 mt-4 text-xs">
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-indigo-400">1. UI Layer (Compose)</div>
    <div class="text-zinc-400 mt-1">Composables observe UI state and emit user actions. Completely decoupled from business logic.</div>
  </div>
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-purple-400">2. Presentation (ViewModel)</div>
    <div class="text-zinc-400 mt-1">Holds screen state using <code>StateFlow</code>. Survives screen rotations and window resize events.</div>
  </div>
  <div class="p-3 rounded-lg bg-zinc-950 border border-zinc-800">
    <div class="font-bold text-emerald-400">3. Data Layer (Repository)</div>
    <div class="text-zinc-400 mt-1">Coordinates local caching (Room), remote APIs (Ktor), and AI models. Highly testable and shareable in KMP!</div>
  </div>
</div>

<div class="text-xs opacity-50 mt-2 text-center">
Source: developer.android.com/topic/architecture
</div>

<!--
Walk through the 3 layers clearly.
This connects Compose, ViewModel, StateFlow, Room, and KMP into one cohesive picture.
-->

---
layout: section
---

# 06 · Your Learning Roadmap

Where to start if you are a student or beginner

---

# The 4-Step Learning Path for 2026

If you want to build apps or land an Android role, follow this progression:

<RoadmapVisual />

<div class="grid grid-cols-2 gap-6 mt-4 text-xs">

<div class="p-3 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-indigo-400 mb-1">Phase 1 &amp; 2: The Core Foundation</div>
  <div class="text-zinc-300 leading-relaxed">
    Master Kotlin fundamentals (null safety, lambdas, coroutines) and Jetpack Compose. Focus on <strong>unidirectional data flow</strong> and managing UI state.
  </div>
</div>

<div class="p-3 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-emerald-400 mb-1">Phase 3 &amp; 4: Shipping Production Apps</div>
  <div class="text-zinc-300 leading-relaxed">
    Connect your UI to a ViewModel, Room database, and an API. Then build a portfolio project that shares logic via KMP or features an on-device ML Kit feature!
  </div>
</div>

</div>

<!--
Give students a clear order of operations.
Do not jump straight into AI or Multiplatform before you can build a clean Compose screen with a ViewModel.
-->

---

# What to Ignore (Save Your Sanity!)

When you search for Android tutorials online, you will find 15 years of legacy advice. **Here is what to safely skip:**

<div class="grid grid-cols-2 gap-6 mt-6 text-sm">

<div class="p-4 rounded-xl bg-rose-950/30 border border-rose-800/40">
  <div class="font-bold text-rose-400 text-base mb-2">❌ Don't Waste Time On:</div>
  <ul class="text-zinc-300 text-xs space-y-2 leading-relaxed">
    <li>• <strong>XML Layouts &amp; findViewById:</strong> Skip them unless dealing with legacy code at an internship.</li>
    <li>• <strong>Old Fragment Managers:</strong> Modern Compose handles navigation without Fragment transactions.</li>
    <li>• <strong>Complex Gradle wizardry early on:</strong> Use the standard project templates and version catalogs.</li>
    <li>• <strong>Trying to learn everything at once:</strong> Get confident with Compose before touching cross-platform.</li>
  </ul>
</div>

<div class="p-4 rounded-xl bg-emerald-950/30 border border-emerald-800/40">
  <div class="font-bold text-emerald-400 text-base mb-2">✅ Do Focus On:</div>
  <ul class="text-zinc-300 text-xs space-y-2 leading-relaxed">
    <li>• <strong>Building complete small apps:</strong> A habit tracker, student schedule app, or campus events feed.</li>
    <li>• <strong>Installing it on your real phone:</strong> Nothing beats the feeling of tapping an app you wrote yourself.</li>
    <li>• <strong>Reading modern official docs:</strong> Google's Android documentation is among the best in tech today.</li>
    <li>• <strong>Publishing on GitHub:</strong> Clean code, READMEs with screenshots, and modern architecture.</li>
  </ul>
</div>

</div>

<!--
Students love this slide because it filters out the noise.
Most students get overwhelmed because they encounter 2017 tutorial content online and think they need to learn XML, Java, and adapters first.
-->

---

# Essential Resources to Bookmark

<div class="grid grid-cols-2 gap-5 mt-6 text-sm">

<a href="https://developer.android.com/courses" target="_blank" class="p-4 rounded-xl bg-zinc-950 border border-zinc-800 hover:border-indigo-500 transition block">
  <div class="font-bold text-indigo-400 mb-1">📘 Android Basics with Compose</div>
  <p class="text-zinc-400 text-xs leading-relaxed">
    Official, free, step-by-step curriculum by Google. Starts from zero Kotlin to building real apps.
  </p>
</a>

<a href="https://github.com/android/nowinandroid" target="_blank" class="p-4 rounded-xl bg-zinc-950 border border-zinc-800 hover:border-indigo-500 transition block">
  <div class="font-bold text-emerald-400 mb-1">🌟 Now in Android (GitHub)</div>
  <p class="text-zinc-400 text-xs leading-relaxed">
    Google's open-source reference production app. Demonstrates 100% modern best practices, testing, and architecture.
  </p>
</a>

<a href="https://kotlinlang.org/multiplatform/" target="_blank" class="p-4 rounded-xl bg-zinc-950 border border-zinc-800 hover:border-indigo-500 transition block">
  <div class="font-bold text-purple-400 mb-1">🌐 Kotlin Multiplatform Portal</div>
  <p class="text-zinc-400 text-xs leading-relaxed">
    Interactive project wizard, documentation, and sample multiplatform apps by JetBrains.
  </p>
</a>

<a href="https://github.com/android/compose-samples" target="_blank" class="p-4 rounded-xl bg-zinc-950 border border-zinc-800 hover:border-indigo-500 transition block">
  <div class="font-bold text-amber-400 mb-1">🎨 Jetpack Compose Samples</div>
  <p class="text-zinc-400 text-xs leading-relaxed">
    Jetsnack, Jetcaster, and Crane. Official sample apps showcasing animations, adaptive UI, and custom graphics.
  </p>
</a>

</div>

<!--
Point students to these 4 links.
If they only bookmark one repo, recommend "Now in Android" on GitHub.
-->

---
layout: two-cols
---

# Thank You! Let's Connect

<div class="pr-6 mt-4">

There has genuinely never been a better time to build for Android.

<v-clicks class="text-sm mt-4 space-y-3">

- The declarative UI toolkit is mature and expressive.
- Kotlin runs everywhere from mobile to servers.
- The platform is expanding into exciting new hardware and on-device intelligence.

</v-clicks>

<div v-click class="mt-8 p-4 rounded-xl bg-indigo-950/40 border border-indigo-800/40">
  <div class="font-bold text-indigo-300 text-base mb-1">Open Floor for Q&amp;A</div>
  <div class="text-xs text-zinc-300">
    Ask me anything: getting started, shipping apps at scale, Compose vs Flutter, career paths, or tech stacks!
  </div>
</div>

</div>

::right::

<div class="flex flex-col items-center justify-center h-full pl-6">

<img
  src="/images/qr-linkedin.svg"
  alt="QR code linking to linkedin.com/in/sanskar10100"
  style="width: 200px; height: 200px;"
  class="rounded-xl shadow-2xl"
/>

<div class="mt-3 text-sm font-semibold text-zinc-100">Sanskar</div>
<div class="text-xs text-zinc-400 font-mono">linkedin.com/in/sanskar10100</div>

<div class="mt-4 text-xs opacity-70 text-center font-mono">
github.com/sanskar10100<br>
roro.io
</div>

</div>

<!--
Wrap up with warmth and encouragement.
Open the floor for questions from students and working devs.
-->
