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
transition: fade-out
---

# Agenda

- **Jetpack Compose** — where the toolkit is now
- **The Android platform** — what shipped, what's changing
- **Kotlin Multiplatform** — sharing beyond Android
- **On-device AI** — Gemini Nano and friends
- **Demos** — live, with video fallbacks

<!--
Speaker notes go here. Press `d` in presenter mode.
-->

---
layout: section
---

# 01 · Jetpack Compose

Where the UI toolkit is now

---
clicks: 3
---

# Quick refresher: what is Compose?

You describe **what the screen should look like**, not the steps to update it. Change the data, the UI redraws itself.

<div class="cmp" :class="'cmp-' + Math.min($clicks, 3)">

<div class="pane pane-view">

**The old way (View system)**

```xml
<TextView
  android:id="@+id/label"
  android:text="Hello" />
```

```kotlin
val label = findViewById<TextView>(R.id.label)
label.text = "Hi, $name"
```

</div>

<div class="pane pane-compose">

**The Compose way**

```kotlin
@Composable
fun Greeting(name: String) {
  Text("Hi, $name")
}
```

</div>

</div>

<div class="cmp-note" :class="{ 'cmp-note-on': $clicks >= 3 }">

No manual "find the view and update it" — just call the function again with new data.

</div>

<!--
Keep this to ~90 seconds. This is a reminder for anyone new, not a lesson.

[click] The old way: find the view by id, then mutate it yourself.

[click] The Compose way: one function, data in, UI out.

[click] Side by side — the point is how much of the first column is bookkeeping.
-->

---
clicks: 3
---

# Now do it for a list

You may still think Views are not that bad. What about a lazy list though?

<div class="cmp cmp-lists" :class="'cmp-' + Math.min($clicks, 3)">

<div class="pane pane-view">

**RecyclerView**

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
// …and then, in the Activity
recyclerView.layoutManager = LinearLayoutManager(this)
recyclerView.adapter = ItemAdapter(names)
```

<div class="pane-aside">Still missing: DiffUtil, so the list can animate when the data changes and you don't have to notify dataset change</div>

</div>

<div class="pane pane-compose">

**LazyColumn**

```kotlin
LazyColumn {
  items(names) { name ->
    Text(name)
  }
}
```

<div class="pane-aside">Significantly better DX here. Also note how it's far easier to build a heterogenous list here compared to Views.</div>

</div>

</div>

<!--
This is the slide that usually lands. Ask the room who has written an adapter.

[click] Walk the RecyclerView side briefly — don't read it, just let the volume speak.

[click] Then LazyColumn. Pause here.

[click] Side by side. The point isn't "less typing", it's that all that code was
bookkeeping the framework can do itself.
-->

---
layout: section
---

# New Shadow API

`Modifier.dropShadow()` & `Modifier.innerShadow()`

---

# The old problem with shadows

- Compose only shipped **elevation-based shadows** — tied to Material Design, one look, directionless
- Matching a shadow from a Figma design meant hacks: extra `Box`es, manual blur, `drawBehind`
- No easy way to do a soft colored glow, a pressed/inset look, or a precise designer spec

<div v-click>

**Compose 1.9 added a real shadow system**, built around two virtual light sources:

- ☀️ **Ambient light** — soft, even, no direction → gentle shadow all around
- 🔦 **Spot light** — directional, from above → a more defined, cast shadow

</div>

---

# `dropShadow` and `innerShadow`

```kotlin
Box(
  modifier = Modifier
    .size(120.dp)
    .dropShadow(
      shape = RoundedCornerShape(24.dp),
      shadow = Shadow(radius = 16.dp, color = Color.Black.copy(alpha = 0.25f))
    )
    .background(Color.White, RoundedCornerShape(24.dp))
)
```

- **`dropShadow`** — sits *behind* the composable → looks raised / elevated
- **`innerShadow`** — sits *inside* the composable's border → looks pressed into the surface
- Layer both together → neumorphism-style effects, glows, soft UI — with a single `Box`, no nesting tricks

<div class="text-xs opacity-60 mt-4">
⚠️ Double-check exact parameter names against the current androidx.compose.ui docs before presenting.
</div>

---

# Play with it

<ShadowPlayground class="mt-4" />

<div class="mt-4 text-sm opacity-70">

Resource: [sinasamaki.com/new-shadow-api-for-jetpack-compose](https://www.sinasamaki.com/new-shadow-api-for-jetpack-compose/)

</div>

<!--
LIVE DEMO — built into the deck, so it works with no wifi and nothing to install.
Drag the sliders and the Kotlin on the right updates with it.

Point to make while dragging: designers hand you these five numbers from Figma.
Before Compose 1.9 there was no clean way to accept them. Now there is.

Credit Sina Samaki out loud — his article is where this API got popularised:
https://www.sinasamaki.com/new-shadow-api-for-jetpack-compose/
-->

---
layout: section
---

# Mesh Gradients

Gradients that aren't just a straight line

---

# The old problem with gradients

- Compose only had **linear**, **radial**, and **sweep** gradients — all defined by a simple shape
- Real designs (and SwiftUI, which got mesh gradients first) often want gradients that flow and blend in multiple directions — like colored light on fabric
- Faking that meant layering multiple blurred shapes on top of each other

<div v-click>

**Mesh gradients** place a grid of colored points in space and blend smoothly between them — closer to painting with color than drawing a shape.

</div>

---

# Mesh gradients in Compose

```kotlin
val painter = rememberMeshGradientPainter {
  setVertex(0, 0, Offset(0f, 0f), Color.Red)
  setVertex(1, 0, Offset(1f, 0f), Color.Yellow)
  setVertex(0, 1, Offset(0f, 1f), Color.Blue)
  setVertex(1, 1, Offset(1f, 1f), Color.Green)
}

Box(Modifier.fillMaxSize().paint(painter))
```

- Built on `drawVertices()` under the hood — the same primitive used to draw smooth 3D-style shading
- Points and colors can be **animated over time** inside the draw scope — no re-allocating shaders per frame
- Great for hero backgrounds, splash screens, loading states — anywhere a flat color feels boring

<div v-click class="mt-4">

**This is now official.** Mesh gradients shipped as a first-party API in **Compose 1.12** (August '26) — the community implementation came first, Google adopted the idea.

</div>

---

# What it looks like

<MeshGradient height="360px" class="mt-4" />

<div class="text-sm opacity-70 mt-4">

Four colour points, blended and slowly drifting. Compose does this on the GPU with `drawVertices`.

</div>

<!--
This is a CSS approximation running live in the slide — good enough to make the point,
and it can't fail on stage.

If you want the real thing: record a screen capture from an emulator running the
Compose 1.12 MeshGradientPainter API and swap this for a <video> tag.
-->

---
layout: two-cols
---

# In a real app

<div class="mt-6 pr-4">

The background isn't a flat colour or a blurred image — it's a **mesh gradient built from the cover art's own colours**.

<v-clicks>

- Here, the mesh gradient is directly derived from the cover image on top.
- Each image is divided into a 4x4 grid. Dominant color is derived from each grid slot.
- Mesh gradient is then constructed using the dominant colors

This was not possible on Compose before the mesh gradient modifier. At least not without a whole lot of effort.

</v-clicks>

</div>

::right::

<div class="flex justify-center items-center h-full">
  <img
    src="/images/mesh-gradient-app.png"
    alt="A reading-list app whose background is a mesh gradient derived from the cover image"
    style="max-height: 430px; width: auto; object-fit: contain;"
    class="rounded-xl shadow-2xl"
  />
</div>

---
layout: section
---

# What else changed recently

A fast lap around the rest of Compose

---

# Performance: it got genuinely faster

Two changes under the hood, no code required from you:

<v-clicks>

- **Pausable composition** — Compose can now stop halfway through building a screen, hand the frame to the system on time, and finish on the next frame. Result: dropped frames largely eliminated. On by default since Dec '25.

- **SlotTable rewrite** — the internal data structure that tracks your UI was rebuilt to copy far less memory. Reordering a long list can recompose **over 2× faster**.

</v-clicks>

<div v-click class="mt-6">

The takeaway for you: **"Compose is slow" is a 2021 complaint.** It isn't true anymore.

</div>

<!--
This is the slide that kills the objection students will have heard secondhand
from a senior dev or a Reddit thread. Worth saying explicitly.
-->

---

# Navigation 3

Navigation was the most-complained-about part of Compose. It got rebuilt.

<v-clicks>

- **Old way:** a navigation "graph" you declared up front, with string routes. The back stack was hidden inside the library — you asked it to do things and hoped.
- **New way:** the back stack is **just a list that you own**. Navigate forward = add to the list. Go back = remove from the list.

</v-clicks>

<div v-click>

```kotlin
val backStack = rememberNavBackStack(HomeKey)

// go somewhere
backStack.add(DetailKey(id = 42))

// go back
backStack.removeLastOrNull()
```

</div>

<div v-click class="mt-4 opacity-80">

Because it's a plain list, things that used to be painful — conditional flows, multi-pane layouts, saving the stack — become ordinary list operations.

</div>

---

# Shared element transitions

The animation where an item **flies from one screen into the next** — a thumbnail growing into a full photo.

<v-clicks>

- Used to require the View system, or a lot of manual work
- Now: mark the same element on both screens with a shared key, and Compose animates between them
- Went **stable in Compose 1.11** (April '26), with debug tooling to see what's matching

</v-clicks>

<div v-click>

```kotlin
Modifier.sharedElement(
  rememberSharedContentState(key = "photo-$id"),
  animatedVisibilityScope = scope,
)
```

</div>

<div v-click class="mt-4 opacity-80 text-sm">

Pairs naturally with Navigation 3 — this is the "app feels polished" feature.

</div>

<!--
DEMO OPPORTUNITY: this is the single most visually impressive thing in the whole
Compose section. Show it, don't describe it.
Fallback video: public/videos/demo-shared-element.mp4
-->

---

# Grid

A real two-dimensional layout, finally.

<v-clicks>

- Compose had `Row`, `Column`, `Box` — and `LazyVerticalGrid` for scrolling lists. Nothing good for **laying out a whole screen** in two dimensions.
- **`Grid`** (Compose 1.11) gives you tracks, gaps and cells — close to CSS Grid if you've done web
- **Named areas** added in 1.12: describe the layout by name instead of by index
- No longer experimental as of 1.13

</v-clicks>

<div v-click class="mt-4 opacity-80">

Why it matters: screen-level structure without nesting six `Row`s inside four `Column`s.

</div>

---

# Compose Hot Reload

<v-clicks>

- Change your UI code → **see it update in the running app**, no rebuild, no restart, no losing your place
- Hit **1.0 stable** in early '26; bundled with Compose Multiplatform from 1.10
- Two modes: trigger it manually, or let it watch your files and reload automatically

</v-clicks>

<div v-click class="mt-6">

This is the change students will feel most on day one. The edit → build → wait → navigate-back-to-the-screen loop is the single biggest tax on learning Android, and it mostly goes away.

</div>

<!--
DEMO OPPORTUNITY: strong one. Change a color / padding live and let the audience
watch it update. Very cheap to demo, very convincing.
Fallback video: public/videos/demo-hot-reload.mp4
-->

---

# Also worth knowing

Not headline features, but they'll show up in tutorials you read:

- **`TextFieldState`** — text fields rewritten around explicit state instead of callbacks; less boilerplate, fewer bugs
- **`retain { }`** — keep state across screen rotation without a full ViewModel
- **Credential Manager integration** — text fields can prompt for passkeys and saved logins directly
- **Material 3 Expressive** — the current default look: bouncier motion, new shapes, expanded FAB/menu system

<div class="text-xs opacity-60 mt-6">
⚠️ Verify version numbers against developer.android.com before the talk — these move fast.
</div>

---
layout: center
---

# Takeaway

Google now calls Android UI development **"Compose first."**

<div class="mt-4 opacity-80">

It's not just the new View system anymore — it's picking up capabilities the old one never had, and the tooling finally matches.

</div>

---
layout: section
---

# 02 · The Android Platform

Android 16 & 17 — what changed, and why you can't ignore it

---

# How Android ships now

<v-clicks>

- **One big release a year** — Android 16 (June '25), Android 17 (June '26)
- **Quarterly updates in between (QPRs)** — these now carry real features, not just bug fixes
- **Google Play sets deadlines** — to publish or update an app, you must *target* a recent version. New apps and updates must target **API 37 (Android 17) by August 2027**.

</v-clicks>

<div v-click class="mt-6">

That last one is the important bit: **you don't get to opt out.** "It works on my phone" stops being true when Play forces you forward and the new rules kick in.

</div>

<!--
Students often assume old tutorials still apply. This slide explains why they don't:
the platform actively deprecates the old way and Play enforces it on a timer.
-->

---
layout: section
---

# The big one: adaptive-first

The phone-shaped assumption is dead

---

# Your app doesn't get to be portrait-only anymore

<v-clicks>

- For years you could write `screenOrientation="portrait"` and lock your app to one shape. Foldables, tablets, and desktop mode made that look broken.
- **Android 16** — started ignoring orientation, resizability and aspect-ratio restrictions on large screens
- **Android 17** — **removes the developer opt-out entirely** on screens wider than 600dp. You can no longer ask to be exempt.

</v-clicks>

<div v-click class="mt-6">

**What this means for you:** assume your app will be resized, rotated, folded, and put in a window next to another app. Build layouts that respond to size, not layouts that assume a shape.

</div>

<div v-click class="text-sm opacity-70 mt-4">

This is exactly what Compose's adaptive layouts and `Grid` exist for — the two halves of the talk connect here.

</div>

---

# Edge-to-edge is mandatory

<div class="grid gap-10 items-center mt-2" style="grid-template-columns: 1.25fr 1fr;">
<div>

<img
  src="/images/edge-to-edge-contrast.gif"
  alt="An app drawing behind the system bars, with and without enough contrast behind the status bar"
  style="max-height: 340px; width: auto; object-fit: contain;"
  class="rounded-lg shadow-xl"
/>

</div>
<div>

Your app draws **behind** the status and navigation bars. Android 16 removed the opt-out.

<v-clicks>

- Handle **window insets**, or your buttons end up underneath the system bars
- Watch **contrast**: system icons sit on top of your content, so light content needs dark icons and vice versa
- `enableEdgeToEdge()` plus `WindowInsets` padding covers most cases

</v-clicks>

</div>
</div>

<div class="text-xs opacity-50 mt-3">
Source: developer.android.com — Android design guidance
</div>

<!--
Point at the status bar area: the same layout with and without a contrast scrim behind the icons.
This is the failure mode students will actually hit — not a crash, just an app that looks broken.
-->

---

# Predictive back

Users can **peek at the previous screen** mid-swipe, before committing to going back.

<div class="grid gap-10 items-center mt-3" style="grid-template-columns: auto 1fr;">
<div>

<video
  src="/videos/demo-predictive-back.mp4"
  autoplay
  loop
  muted
  playsinline
  style="max-height: 330px; width: auto;"
  class="rounded-xl shadow-2xl"></video>

</div>
<div>

<v-clicks>

- Makes navigation feel physical instead of instant — you see where you're going
- Also drives the system's cross-activity and cross-app back animations
- You hook into it via `onBackInvokedCallback` (or `PredictiveBackHandler` in Compose)
- Opt in with `android:enableOnBackInvokedCallback="true"` in the manifest

</v-clicks>

<div v-click class="mt-4 text-sm opacity-80">

Like edge-to-edge, a small change that makes an app instantly look current — or instantly look neglected.

</div>

</div>
</div>

<!--
The video loops on its own — let it run while you talk through the points.
The gesture is the whole point, so give the room a moment to watch before you start.
-->

---

# Live Updates

A notification type for things **happening right now** — food delivery, ride tracking, a workout in progress.

<div class="grid gap-6 mt-4" style="grid-template-columns: 1fr 1fr;">
<div>

<img
  src="/images/live-update-shade.png"
  alt="A food delivery Live Update in the notification shade, showing an order progress bar and a Track Order button"
  style="width: 100%; max-height: 235px; object-fit: contain;"
  class="rounded-lg shadow-xl"
/>

<div class="text-xs opacity-60 mt-2">In the shade: progress, ETA, and an action</div>

</div>
<div>

<img
  src="/images/live-update-chip.jpg"
  alt="The same Live Update collapsed into a status bar chip reading 28 mins"
  style="width: 100%; max-height: 235px; object-fit: contain;"
  class="rounded-lg shadow-xl"
/>

<div class="text-xs opacity-60 mt-2">Collapsed into a status bar chip — visible from any screen</div>

</div>
</div>

<div class="text-sm opacity-70 mt-4">

Promoted to the **lock screen and status bar** instead of buried in the shade. Shipped in Android 16's quarterly update, alongside the Material 3 Expressive refresh — the OS is making "ongoing activity" a first-class concept.

</div>

<!--
These are real screenshots from my own phone — a food delivery order in progress.
Tap the chip and it expands back into the full notification.
-->

---

# Privacy keeps tightening

Not one release — a direction. Each of these lands across Android 16 and 17.

<v-clicks>

- **Local network access needs permission** — the new `ACCESS_LOCAL_NETWORK` runtime permission. Opt-in in Android 16; **required** once you target Android 17. (Casting, smart-home, local servers.)
- **OTP texts are held back for 3 hours** — started in Android 16 QPR2 (Dec 2025) for SMS Retriever messages; Android 17 extends it to WebOTP and ordinary OTP texts. Use **SMS Retriever** or **SMS User Consent** instead of reading the inbox.
- **Encrypted Client Hello (ECH)** — hides which site you're connecting to from the network. Automatic at targetSdk 37, *if* your HTTP library and the server both support it.

</v-clicks>

<div v-click class="mt-4 opacity-80">

The pattern across every release: **permissions get narrower, and granted later.** Design assuming the user says no.

</div>

<div class="text-xs opacity-50 mt-3">
Source: developer.android.com — Android 17 behavior changes
</div>

---
layout: center
---

# Takeaway

The platform has opinions now.

<div class="mt-4 opacity-80">

Assume permissions get denied, assume your app gets resized, assume you can't opt out.
<br>
Most of modern Android development is working *with* those assumptions instead of against them.

</div>

---
layout: section
---

# 03 · Kotlin Multiplatform

One language, many targets

---

# What is KMP?

<div class="text-xl mt-2">

An open-source technology from JetBrains for **sharing code across Android, iOS, desktop, web and server** — while keeping the advantages of native development.

</div>

<v-clicks>

- You write the shared parts **once, in Kotlin**
- You decide **how much** to share — a single function, all your logic, or the UI too
- The shared code compiles into whatever each platform normally consumes: a `.jar`/`.aar` for Android, a real framework for iOS

</v-clicks>

<div v-click class="mt-6 text-sm opacity-70">

Shipping in production at **Google Workspace, Duolingo, McDonald's, Forbes, Booking.com, Sony** — JetBrains reports KMP's presence among the top 10K apps doubled year over year.

</div>

<div class="text-xs opacity-50 mt-3">
Source: kotlinlang.org/multiplatform
</div>

<!--
Frame it as: this isn't a new language or a new UI framework. It's the Kotlin you
already write, pointed at more than one platform.
-->

---

# Why Kotlin can do this at all

Kotlin isn't one compiler — it's **one front end with several back ends.**

<div class="grid grid-cols-2 gap-x-10 gap-y-3 mt-4">
<div>

**Kotlin/JVM** → JVM bytecode
<div class="text-sm opacity-70">Android, and every server framework you know</div>

</div>
<div>

**Kotlin/Native** → machine code, via **LLVM**
<div class="text-sm opacity-70">iOS, macOS, Linux, Windows — no VM involved</div>

</div>
<div>

**Kotlin/JS** → JavaScript
<div class="text-sm opacity-70">Browsers and Node</div>

</div>
<div>

**Kotlin/Wasm** → WebAssembly
<div class="text-sm opacity-70">The newest target — how Compose runs on the web</div>

</div>
</div>

<div v-click class="mt-6">

**That's the whole trick.** The same `.kt` file goes through a different back end per platform. KMP is the build system organising that, not a runtime sitting underneath your app.

</div>

<!--
This is the slide that makes KMP click for people. Everything else follows from it.
Kotlin/Native produces a real Apple framework — Xcode treats it like any other one.
-->

---

# It's not Flutter, and it's not React Native

<div class="grid grid-cols-3 gap-5 mt-4 text-sm">
<div class="p-3 rounded-lg" style="background: rgba(255,255,255,0.05);">

**React Native**

Your code is **JavaScript**, running in a JS engine you ship with the app. It asks the platform to draw native components.

<div class="mt-2 opacity-60">Runtime in the middle</div>

</div>
<div class="p-3 rounded-lg" style="background: rgba(255,255,255,0.05);">

**Flutter**

Your code is **Dart**, compiled ahead of time. Flutter brings its **own rendering engine** and draws every pixel itself.

<div class="mt-2 opacity-60">Own UI, not the platform's</div>

</div>
<div class="p-3 rounded-lg" style="background: rgba(99,102,241,0.18);">

**Kotlin Multiplatform**

Your code is **Kotlin**, compiled to each platform's native format. No VM, no bridge, no shipped runtime.

<div class="mt-2 opacity-60">UI is your choice</div>

</div>
</div>

<v-clicks>

- The other two are **UI frameworks first** — adopting them means adopting their way of drawing screens
- KMP is a **code-sharing tool first**. Sharing UI is opt-in, via Compose Multiplatform
- So you can call `UIKit`, `Camera2`, or any platform API **directly**, with no wrapper to wait for

</v-clicks>

<div v-click class="mt-4 text-sm opacity-75">

**Fair warning:** if you *do* use Compose Multiplatform for UI on iOS, it renders with **Skia onto a Metal layer** — it draws its own pixels, much like Flutter. The difference is that KMP doesn't make you.

</div>

<!--
Students will have heard of Flutter and RN. Anchor KMP against them or it sounds
like a third thing doing the same job. The honest trade-off: KMP gives you native
fidelity and incremental adoption; Flutter gives you one UI everywhere.

Don't skip the last line. If someone in the room knows Compose Multiplatform is
Skia-based, glossing over it costs you the whole section's credibility.
-->

---

# You choose how much to share

<div class="flex justify-center mt-2">

<img
  src="/images/kmp/kmp-graphic.png"
  alt="Three levels of Kotlin Multiplatform adoption: share a piece of logic, share logic and keep the UI native, or share up to 100% of the code"
  style="width: 100%; max-height: 300px; object-fit: contain;"
  class="rounded-lg"
/>

</div>

<div class="text-sm opacity-70 mt-4">

You are not signing up for all of it on day one. Most teams start in the left box — one module, one problem — and move right only if it pays off.

</div>

<div class="text-xs opacity-50 mt-2">
Graphic: kotlinlang.org — Kotlin Multiplatform overview
</div>

<!--
This is the most reassuring slide in the section. "Try it on one file" is a much
easier sell to a student than "rewrite your app".
-->

---

# Starting a project

<div class="grid gap-10 mt-2" style="grid-template-columns: 1.1fr 1fr;">
<div>

<v-clicks>

- **IntelliJ IDEA** or **Android Studio** with the Kotlin Multiplatform plugin: `File → New → Project → Kotlin Multiplatform`
- Pick your targets (Android, iOS, desktop, web) and whether you want to **share the UI**
- You need a **Mac with Xcode** to build and run the iOS side — that requirement doesn't go away
- The wizard hands you a working two-platform app to start editing

</v-clicks>

</div>
<div>

```text
GreetingKMP/
├── composeApp/       shared code
│   └── src/
│       ├── commonMain/    ← shared
│       ├── androidMain/   ← Android only
│       └── iosMain/       ← iOS only
├── iosApp/           Xcode project
└── build.gradle.kts
```

<div class="text-xs opacity-60 mt-2">
The Xcode project is a real Xcode project. iOS developers keep their tools.
</div>

</div>
</div>

<!--
Worth saying out loud: the Mac requirement is the practical blocker for students.
If they only have Windows, they can still do Android + desktop + web targets.
-->

---

# How you write code: source sets

<div class="grid gap-8 items-center mt-2" style="grid-template-columns: 1fr 1fr;">
<div>

<img
  src="/images/kmp/multiplatform-executables-diagram.svg"
  alt="Diagram: commonMain compiles to all targets, appleMain to Apple targets, iosArm64Main to iPhone only, together producing native executables"
  style="width: 100%; max-height: 260px; object-fit: contain;"
  class="rounded-lg bg-white p-2"
/>

</div>
<div>

A **source set** is just a folder with rules about which targets it compiles for.

<v-clicks>

- `commonMain` — compiled for **every** target. Only Kotlin and multiplatform libraries here
- `androidMain` — Android only. `Context`, `Build`, any Java library
- `iosMain` — iOS only. `UIKit`, `NSUserDefaults`, Foundation
- Platform folders can see `commonMain`. **`commonMain` cannot see them** — that's what keeps shared code portable

</v-clicks>

</div>
</div>

<div class="text-xs opacity-50 mt-2">
Diagram: kotlinlang.org — Understand the project structure
</div>

---

# When shared code needs a platform API: `expect` / `actual`

<div class="grid gap-6 mt-2" style="grid-template-columns: 1fr 1fr;">
<div>

**commonMain** — declare the shape, no body

```kotlin
expect fun platformName(): String
```

<div class="text-sm opacity-70 mt-3">

The compiler now **requires** every target to supply one. Miss it and the build fails — not the app.

</div>

</div>
<div>

**androidMain**

```kotlin
actual fun platformName() =
  "Android ${Build.VERSION.SDK_INT}"
```

**iosMain**

```kotlin
actual fun platformName() =
  UIDevice.currentDevice.systemName()
```

</div>
</div>

<div v-click class="mt-4 text-sm opacity-80">

Same idea as an interface, enforced at compile time across platforms. JetBrains' own advice: reach for **plain interfaces and dependency injection** first, and keep `expect`/`actual` for the places you genuinely need it.

</div>

<div class="text-xs opacity-50 mt-2">
Source: kotlinlang.org — Expected and actual declarations
</div>

---

# Level 1 — share a piece of logic

The smallest useful thing: one function, no UI, no architecture change.

```kotlin
// shared/src/commonMain/kotlin/Validation.kt
fun isValidUpiId(input: String): Boolean {
  val parts = input.split("@")
  return parts.size == 2 && parts.all { it.isNotBlank() }
}
```

<div class="grid grid-cols-2 gap-6 mt-4">
<div>

**Android calls it as Kotlin**

```kotlin
if (isValidUpiId(text)) submit()
```

</div>
<div>

**iOS calls it as Swift**

```swift
if ValidationKt.isValidUpiId(input: text) {
    submit()
}
```

</div>
</div>

<div v-click class="mt-4 text-sm opacity-80">

Validation rules, pricing maths, date handling — the code where **the two platforms silently disagreeing is a real bug.**

</div>

---

# Level 2 — share all the logic, keep the UI native

```kotlin
// commonMain — one view model, both platforms
class CounterViewModel : ViewModel() {
  private val _count = MutableStateFlow(0)
  val count: StateFlow<Int> = _count.asStateFlow()

  fun increment() { _count.value += 1 }
}
```

<div class="grid grid-cols-2 gap-6 mt-3">
<div>

**Android — Jetpack Compose**

```kotlin
val count by vm.count.collectAsState()
Text("Count: $count")
Button(onClick = vm::increment) { Text("+") }
```

</div>
<div>

**iOS — SwiftUI**

```swift
Text("Count: \(model.count)")
Button("+") { model.increment() }
```

</div>
</div>

<div v-click class="mt-3 text-sm opacity-80">

Networking, storage, state — written once. Every screen still looks and behaves exactly like its platform, because it *is* its platform.

</div>

---

# Level 3 — share the UI too, with Compose Multiplatform

```kotlin
// commonMain — this screen runs on Android, iOS, desktop and web
@Composable
fun CounterScreen(vm: CounterViewModel) {
  val count by vm.count.collectAsState()

  Column(horizontalAlignment = Alignment.CenterHorizontally) {
    Text("Count: $count", style = MaterialTheme.typography.headlineMedium)
    Button(onClick = vm::increment) { Text("Add one") }
  }
}
```

<v-clicks>

- It's the **same Compose** you learned earlier in this talk — `Column`, `Text`, `Button`, state and all
- On iOS it renders through **Kotlin/Native**, not a web view and not a bridge
- You can still drop to a native view for any single screen that needs it

</v-clicks>

<!--
Tie it back to section 01 explicitly — the Compose knowledge transfers, which is
the strongest argument for a student to learn Compose properly.
-->

---

# "But isn't cross-platform slow?"

<div class="flex justify-center mt-1">

<img
  src="/images/kmp/cmp-ios-performance.png"
  alt="JetBrains benchmark: scrolling FPS for SwiftUI versus Compose Multiplatform on iPhone 13 and iPhone 16, automatic and manual scrolling, showing near-identical results"
  style="width: 100%; max-height: 240px; object-fit: contain;"
  class="rounded-lg"
/>

</div>

<v-clicks class="text-sm">

- Scrolling FPS on iPhone 13 and 16 — every pair **overlaps inside the error bars**. The claim is *comparable*, not faster
- It's fast because Compose on iOS skips the UIKit view tree entirely and draws through **Skia → Metal**
- Read it as "no longer the reason to say no", not as proof of a win

</v-clicks>

<div class="text-xs opacity-50 mt-3">
Chart: JetBrains, via kotlinlang.org. Vendor's own benchmark — no published methodology or raw numbers.
</div>

<!--
If someone asks "isn't this marketing?" — yes, partly, and say so:
- JetBrains benchmarking JetBrains, published as a chart with no methodology
- Their public benchmark suite on GitHub has no SwiftUI comparison in it at all
- Both bars sit under 60fps, so the test scene was heavy by design
- "Automatic scroll" is a programmatic fling — no touch handling, best-case pacing

The technical reason it holds up: Compose draws its own pixels via Skia on Metal,
so it never pays for UIKit view lifecycle or Auto Layout on a long list.
-->

---

# Where this is going

<div class="grid gap-8 mt-4" style="grid-template-columns: 1fr 1fr;">
<div>

**Tooling caught up**

<v-clicks>

- Google ships **Room, DataStore, ViewModel and Lifecycle** as multiplatform libraries
- Compose Multiplatform covers iOS, desktop and web
- JetBrains points **Junie**, its AI coding agent, at KMP tasks — scaffolding targets, writing `actual` implementations

</v-clicks>

</div>
<div>

**What to actually do**

<v-clicks>

- Learn **Kotlin and Compose** properly first — both transfer directly
- Then try sharing **one** thing: a validator, a parser, an API client
- Don't start by trying to share a whole app

</v-clicks>

</div>
</div>

<div class="text-xs opacity-50 mt-4">
Sources: kotlinlang.org/multiplatform · kotlinlang.org — KMP overview
</div>

---
layout: center
---

# Takeaway

Kotlin compiles to more than one thing. KMP is what you get when you take that seriously.

<div class="mt-4 opacity-80">

Share what's genuinely the same on both platforms, keep native what should feel native.
<br>
The escape hatch is always there — that's the part Flutter and React Native can't offer.

</div>
