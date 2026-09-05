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
layout: two-cols-header
---

# Quick refresher: what is Compose?

You describe **what the screen should look like**, not the steps to update it. Change the data, the UI redraws itself.

::left::

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

::right::

**The Compose way**

```kotlin
@Composable
fun Greeting(name: String) {
  Text("Hi, $name")
}
```

<div v-click class="mt-4 opacity-80 text-sm">

No manual "find the view and update it" — just call the function again with new data.

</div>

<!--
Keep this to ~90 seconds. This is a reminder for anyone new, not a lesson.
If everyone already knows Compose, skip to the next slide.
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

<MeshGradient height="240px" class="mt-4" />

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

# Two changes you'll see immediately

<div class="grid grid-cols-2 gap-8 mt-6">
<div>

### Edge-to-edge is mandatory

Your app draws **behind** the status and navigation bars, full screen.

Android 16 removed the opt-out.

If you don't handle insets, your buttons end up underneath the system bars.

</div>
<div>

### Predictive back

Users can **peek at the previous screen** mid-swipe before committing to going back.

Makes navigation feel physical instead of instant.

You hook into it via `onBackInvokedCallback`.

</div>
</div>

<div v-click class="mt-8 opacity-80">

Both are small code changes that make an app instantly look current — or instantly look neglected.

</div>

<!--
DEMO / VISUAL: show a before-and-after of an app that ignores insets vs one that handles them.
Predictive back is best shown as a short screen recording — the gesture doesn't read in a screenshot.
Fallback video: public/videos/demo-predictive-back.mp4
-->

---

# Live Updates

A notification type for things **happening right now**.

- Food delivery, ride tracking, a workout in progress
- Shows live progress on the **lock screen and status bar**, not buried in the shade
- Arrived in Android 16's quarterly update, alongside a system-wide Material 3 Expressive visual refresh

<div class="text-sm opacity-70 mt-4">

The OS is carving out space for "ongoing activity" as a first-class concept.

</div>

<!-- TODO: screenshot of a Live Update on the lock screen → public/images/live-updates.png -->

---

# Android 17: privacy keeps tightening

<v-clicks>

- **Local network access is blocked by default** — if your app wants to talk to other devices on the same Wi-Fi, it now needs permission. (Casting, smart-home, local servers.)
- **OTP text messages are delayed for 3 hours** for most apps — reading someone's one-time code out of their inbox was too easy to abuse. Use the **SMS Retriever** or **SMS User Consent** APIs instead.
- **Encrypted Client Hello (ECH)** — hides which site you're connecting to from the network, at the platform level

</v-clicks>

<div v-click class="mt-6 opacity-80">

The pattern across every release: **permissions get narrower, and granted later.** Design assuming the user says no.

</div>

---

# Android 17: things that quietly break old code

Worth recognising if you hit them — you don't need to memorise these:

<v-clicks>

- **`static final` fields are now truly final** — libraries that used reflection to patch constants at runtime will crash
- **Widgets have a memory budget** — oversized bitmaps in a widget now throw a fatal error instead of silently struggling
- **Fewer activity restarts** — keyboard, UI mode, and colour-mode changes no longer recreate your screen; you get a callback instead. Faster, but surprising if you relied on the restart.
- **New lock-free message queue** — a free performance win, unless you were reaching into private framework internals

</v-clicks>

<div v-click class="text-sm opacity-70 mt-6">

Theme: the platform is closing doors that apps were sneaking through.

</div>

---

# One more, and it sets up the next section

<div class="text-xl mt-8">

Apps targeting Android 17 must **declare that they use the NPU** — the neural processing unit — before they can touch it directly.

</div>

<div v-click class="mt-8 opacity-80">

The fact that the *operating system* now has a permission-shaped concept for "AI chip access" tells you where this is going.

</div>

<div v-click class="mt-6">

Google's own framing for Android 17: the start of a transition to an **intelligence system**, and an **adaptive-first** development standard.

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
