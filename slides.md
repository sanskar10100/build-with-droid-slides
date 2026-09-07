---
theme: seriph
title: The State of Android Development
info: |
  A talk on the current state of Android development —
  Compose, the platform, Kotlin Multiplatform, and on-device AI.
class: text-center
transition: slide-left
mdc: true
drawings:
  persist: false
---

# The State of Android Development

Compose · Platform · KMP · On-Device AI

<div class="pt-12 opacity-70 text-sm">
  Press <kbd>space</kbd> to start
</div>

---
layout: two-cols
---

# Hi, I'm Sanskar

**Senior Software Engineer at [Roro](https://roro.io)**, a product studio.
I've shipped Android apps used by millions of people across fintech, consumer, and health.

<v-clicks>

- **2016** — Built my first Android app in Java with XML layouts, `RelativeLayout`, and runtime crashes on screen rotation.
- **Today** — Pure Kotlin, declarative Jetpack Compose, and reactive unidirectional data flow.
- **Lately** — Sharing production code across platforms with KMP, and exploring on-device AI capabilities.

</v-clicks>

<div v-click class="mt-5 text-sm opacity-85 leading-relaxed">
If you are learning Android today, you are stepping in at the best possible time. The modern stack is expressive, clean, and genuinely fun to build with. This talk is the practical map I wish I had when starting out.
</div>

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
  <div class="font-bold text-amber-400 text-base mb-1">04 · On-Device AI</div>
  <div class="text-zinc-300">Running Gemini Nano and small models locally with AICore and ML Kit GenAI.</div>
</div>

</div>

<div class="mt-6 p-3.5 rounded-xl bg-indigo-950/40 border border-indigo-800/40 text-xs text-indigo-200 flex justify-between items-center">
  <span><strong>Bonus Sections:</strong> Modern Architecture (how it connects) &amp; A Practical Student Learning Roadmap</span>
  <span class="font-mono opacity-80">~50 min + Q&amp;A</span>
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

<div class="pane-aside">You describe what the screen looks like for a given state. When state changes, Compose redraws.</div>

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

# Why Developers Refused to Look Back: Lists

If you ever learned Android before 2021, you remember the boilerplate of building a scrolling list.

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

<div class="pane-aside">No adapters, no viewholders, no XML inflation. Heterogeneous layouts are just another Kotlin block.</div>

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

- **Why it matters:** Previously, Compose only supported Material elevation shadows—rigid, one look, directionless.
- **Figma fidelity:** Designers hand you precise Figma specs (soft colored glows, neumorphic bevels, inset pressed states).
- **Zero hackiness:** You no longer need nested `Box` hierarchies or manual Canvas blur shaders to match design specs.

</v-clicks>

<!--
Acknowledge that early Compose lacked visual nuance for non-Material designs.
With dropShadow and innerShadow, you can implement exact Figma specs in a single modifier chain.
-->

---

# Interactive: Custom Shadows in Action

<ShadowPlayground class="mt-4" />

<div class="mt-4 text-xs opacity-70">
Credit: Sina Samaki (sinasamaki.com/new-shadow-api-for-jetpack-compose)
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
val painter = rememberMeshGradientPainter {
  setVertex(0, 0, Offset(0f, 0f), Color.Red)
  setVertex(1, 0, Offset(1f, 0f), Color.Yellow)
  setVertex(0, 1, Offset(0f, 1f), Color.Blue)
  setVertex(1, 1, Offset(1f, 1f), Color.Green)
}

Box(Modifier.fillMaxSize().paint(painter))
```

<v-clicks class="text-sm mt-3">

- Shipped officially in **Compose 1.12** via GPU-accelerated `drawVertices`.
- Vertex coordinates and colors can be animated smoothly over time.
- Ideal for hero cards, dynamic album art, and ambient backgrounds.

</v-clicks>

::right::

<div class="pl-4 pt-2">
  <MeshGradient height="260px" class="mt-2" />
  <div class="text-xs opacity-70 mt-3 text-center">
    4 color points blended across a coordinate grid on the GPU
  </div>
</div>

<!--
Mesh gradients give apps that modern, fluid lighting feel (like iOS Lock Screen or Spotify player backgrounds).
Mention that this is now built into first-party Compose without third-party OpenGL hacks.
-->

---
layout: two-cols
---

# Mesh Gradients in Production

<div class="mt-4 pr-4">

**Dynamic Ambient UI from Cover Art**

In this reading app, the background is not a static flat color or a blurry box.

<v-clicks class="text-sm mt-4">

- The book cover image is sampled across a 4x4 coordinate grid.
- Dominant colors are extracted from each quadrant.
- Compose constructs a live mesh gradient that matches the artwork seamlessly.
- Produces an organic, magazine-quality aesthetic with minimal performance overhead.

</v-clicks>

</div>

::right::

<div class="flex justify-center items-center h-full">
  <img
    src="/images/mesh-gradient-app.png"
    alt="Reading app showing ambient mesh gradient derived from book cover"
    style="max-height: 420px; width: auto; object-fit: contain;"
    class="rounded-xl shadow-2xl"
  />
</div>

<!--
Show the real-world screenshot.
Point out how dynamic styling helps apps stand out on the Play Store.
-->

---
layout: two-cols
---

# Shared Element Transitions

Connecting screens with continuous visual motion

<div class="pr-3">

<p class="text-xs text-zinc-300 leading-relaxed mb-2">
Instead of a jarring cut between screens, shared elements morph seamlessly across navigation routes.
</p>

```kotlin
// Inside SharedTransitionLayout
Modifier.sharedElement(
  state = rememberSharedContentState(key = "snack-${item.id}"),
  animatedVisibilityScope = animatedVisibilityScope,
)
```

<v-clicks class="text-xs space-y-1.5 mt-2">

- **Shared Key:** Pairs composables across routes (`"snack-${item.id}"`).
- **Fluid Animation:** Bounds, scale, and clip shape animate continuously.
- **Stable in Compose 1.11+** with layout inspection tooling.

</v-clicks>

</div>

::right::

<div class="flex flex-col items-center justify-center h-full pl-2">

<img
  src="/images/basic_shared_element_jetsnack.gif"
  alt="Official Google Jetsnack shared element transition animation"
  style="max-height: 275px; width: auto; object-fit: contain;"
  class="rounded-xl shadow-xl border border-zinc-800"
/>

<div class="text-[10px] opacity-60 mt-1.5 text-center">
Official Jetsnack sample — thumbnail expands into hero banner
</div>

</div>

<!--
This is one of the most requested features in modern mobile UI.
Notice the key: "item-${item.id}". When the user taps, Compose links the thumbnail on Screen A with the hero image on Screen B and smoothly morphs the bounds.
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

# Tooling & Performance Under the Hood

Compose is not just syntax; the runtime has matured tremendously.

<div class="grid grid-cols-3 gap-5 mt-6 text-sm">

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-indigo-400 text-base mb-2">⚡ Pausable Composition</div>
  <p class="text-zinc-300 text-xs leading-relaxed">
    If rendering a complex screen takes longer than the 16ms frame deadline, Compose pauses, yields to the Android OS to deliver the frame on time, and resumes on the next frame. Dropped frames are drastically reduced.
  </p>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-emerald-400 text-base mb-2">📦 SlotTable Rewrite</div>
  <p class="text-zinc-300 text-xs leading-relaxed">
    The internal data structure tracking composables was re-architected to avoid unnecessary memory allocations. Reordering long lists and animated layouts recomposes up to <strong>2× faster</strong>.
  </p>
</div>

<div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
  <div class="font-bold text-purple-400 text-base mb-2">🔥 Compose Hot Reload</div>
  <p class="text-zinc-300 text-xs leading-relaxed">
    Edit UI code in Android Studio and watch it update immediately on your running emulator or phone—without restarting the app and without losing your navigation state or typed form inputs.
  </p>
</div>

</div>

<div v-click class="mt-6 p-3 rounded-xl bg-zinc-900 border border-zinc-800 text-xs text-center text-zinc-300">
<strong>The takeaway:</strong> The outdated 2021 criticism that "Compose is slower than XML" is completely obsolete today.
</div>

<!--
Address the elephant in the room: students often read outdated Reddit threads claiming Compose has performance issues.
Explain that modern Compose with baseline profiles and pausable composition is exceptionally fast.
-->

---
layout: center
---

# Compose Takeaway

Jetpack Compose is now the default, undisputed standard for Android UI.

<div class="mt-4 opacity-80 text-base max-w-xl mx-auto leading-relaxed">
If you invest time into one concept, master <strong>State Management</strong> (<code>remember</code>, <code>mutableStateOf</code>, and <code>StateFlow</code>).
<br><br>
Once you understand how state drives the UI, building complex, expressive animations and responsive layouts becomes second nature.
</div>

---
layout: section
---

# 02 · The Android Platform

Adaptive screens, modern UX, and platform behavior

---

# How Android Ships Today

<v-clicks>

- **Predictable Annual Releases:** Android 15 (2024), Android 16 (2025), Android 17 (2026).
- **Quarterly Platform Releases (QPRs):** Google now rolls out meaningful developer APIs and system enhancements throughout the year, not just in summer releases.
- **Google Play Target SDK Policy:** Every year, Google Play mandates that updates target a recent API level to preserve user security and battery life.

</v-clicks>

<div v-click class="mt-6 p-4 rounded-xl bg-zinc-950 border border-zinc-800 text-sm">
  <div class="font-bold text-indigo-400 mb-1">What this means for you:</div>
  <div class="text-zinc-300 leading-relaxed">
    You cannot rely on old habits forever. The platform pushes apps forward on a strict schedule. Understanding modern platform behavior is what differentiates a junior coder from a professional engineer.
  </div>
</div>

<!--
Explain why targeting modern SDKs matters. It is not just about version numbers; it is about building apps that follow modern battery, privacy, and display rules.
-->

---

# Beyond the 5-Inch Phone

The assumption that Android runs solely on a vertical 5-inch phone is gone.

<v-clicks class="text-sm">

- **Form Factors Everywhere:** Foldables (Galaxy Z Fold, Pixel Fold), tablets, ChromeOS laptops, and Samsung DeX / Desktop Mode are widespread.
- **No More Orientation Locks:** Starting with Android 16 and 17, the OS actively **ignores** `screenOrientation="portrait"` and non-resizable flags on displays wider than 600dp.
- **The User Can Resize Anytime:** Your app will be snapped into split-screen, unfolded mid-use, or floated in a desktop window.

</v-clicks>

<div v-click class="mt-5 p-3.5 rounded-xl bg-amber-950/40 border border-amber-800/40 text-xs text-amber-200 leading-relaxed">
<strong>Key Mindset Shift:</strong> Never assume your screen has a fixed width or height. Build responsive layouts that adapt fluidly to whatever window size the user gives you.
</div>

<!--
Explain that foldables and tablets aren't edge cases anymore.
If someone unfolds a phone while your app is open, your layout must reflow cleanly without restarting or crashing.
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
Phone: navigates to full screen. Foldable/Tablet: renders dual panes side-by-side automatically.
</div>

</div>

::right::

<div class="pl-2">
  <AdaptiveVisual />
  <div class="text-[10px] opacity-60 text-center mt-1">
    Click the buttons above to preview how your UI reflows across breakpoints!
  </div>
</div>

<!--
Point out that you do NOT need to write separate apps or duplicate Activities.
ListDetailPaneScaffold handles the transition and back navigation between single-pane and dual-pane automatically.
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

# Predictive Back Gestures

Navigation that feels physical and tactile

<div class="grid gap-8 items-center mt-3" style="grid-template-columns: auto 1fr;">
<div>

<video
  src="/videos/demo-predictive-back.mp4"
  autoplay
  loop
  muted
  playsinline
  style="max-height: 330px; width: auto;"
  class="rounded-xl shadow-2xl"></video>

<div class="text-[11px] opacity-60 mt-2 text-center">
User peeking at the previous screen
</div>

</div>
<div>

<v-clicks class="text-sm">

- Users can **peek** at the previous screen mid-swipe before committing.
- Eliminates accidental exits—just reverse the gesture to cancel.
- Seamlessly supported in Compose with `PredictiveBackHandler`.

</v-clicks>

```kotlin
PredictiveBackHandler { progressFlow ->
  progressFlow.collect { backEvent ->
    sheetOffset = backEvent.progress
  }
}
```

</div>
</div>

<!--
Let the video loop for a second so attendees see the fluid motion.
Predictive back gives apps that premium, native feel that users instantly notice.
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
layout: section
---

# 04 · On-Device AI

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
