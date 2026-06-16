# design-spec.md — FlexHaus Comeback-Booking Page

**Designer:** Mateo (Red-Orange — conversion-led booking-flow + WebGL experience designer)
**Date:** 2026-06-16
**Inputs:** Tara's `diagnosis.md` (§5.7 re-entry point, §8 key takeaways), `context_rich.md` (§3 brand, §5 three.js spec, §2 gym data)
**Handoff to:** Kojo (Blue Maker)
**Status:** Complete, ready for build

---

## Design Overview

**What this design solves:** A disengaged-but-still-paying FlexHaus member receives a win-back message, taps a link, and lands on a page that converts their latent guilt into a single, pre-filled, frictionless comeback-class booking — in one tap. The 3D WebGL experience is not decoration; it is the emotional arc that carries the member from "I haven't been in weeks" to "I'm booked for Tuesday." The page works identically as a conversion tool with or without 3D.

**The ONE metric it serves:** Comeback-class bookings (at-risk members who book AND attend a class within 30 days of receiving the first win-back message — per Tara §7).

**Core insight from Tara's diagnosis driving every decision:**
- 195 active at-risk members, €105,564/year at stake (§3.2)
- The easiest re-entry point is **Strength with Marta, Tuesday 9am or 12pm** — highest return rate (64.4% Marta, 62.2% Tuesday, 65.6% at 9am) intersecting with lowest no-show rates (§5.7)
- The one provable stat: **"Members who skip the second class are 2× more likely to quit"** (1.98× lift, §2.2)
- Three behavioural cohorts for messaging tone, but one unified booking experience (§8)

---

## 1. Page Architecture — Section-by-Section Wireframes

The page is a **single scrollable column** ~400vh tall. A fixed full-viewport `<canvas>` sits behind all DOM content (`aria-hidden="true"`, `role="presentation"`). Five DOM sections are absolutely positioned over the canvas, each fading in/out based on scroll progress. The canvas drives 3D scene changes; the DOM drives content, accessibility, and conversion.

### Full-Page ASCII Wireframe (mobile viewport, ~375×812)

```
┌─────────────────────────────────┐
│                                 │
│     ████████████████████████     │  ← Canvas (fixed, full-viewport,
│     ██ 3D: Dark field,      ██     │           aria-hidden="true")
│     ██ slow heartbeat line  ██     │
│     ██ in Volt-green        ██     │
│     ████████████████████████     │
│                                 │
│  ┌─────────────────────────┐    │  ← DOM overlay §1 (0-25% scroll)
│  │                         │    │
│  │  YOUR SPOT'S STILL      │    │     H1 · Anton · Chalk
│  │  HERE.                  │    │     2.5-4.5rem
│  │                         │    │
│  │  No guilt. No catch-up. │    │     Body · Inter · Chalk 80%
│  │  Just the class you     │    │
│  │  loved, waiting.        │    │
│  │                         │    │
│  │  ┌───────────────────┐  │    │
│  │  │ BOOK YOUR COMEBACK│  │    │     CTA · Volt bg · Carbon text
│  │  │ CLASS             │  │    │     Anton/Inter bold · 1.125rem
│  │  └───────────────────┘  │    │
│  │                         │    │
│  │  ↓ scroll               │    │
│  └─────────────────────────┘    │
│                                 │
├─────────────────────────────────┤  ← ~100vh mark
│                                 │
│  ████████████████████████████    │  ← Canvas: camera fly-through
│  ██ Gym rig, chalk dust,  ██    │     gym space, pulse accelerating
│  ██ pulse accelerating    ██    │
│  ████████████████████████████    │
│                                 │
│  ┌─────────────────────────┐    │  ← DOM overlay §2 (25-50% scroll)
│  │                         │    │
│  │  It's not a setback.    │    │     H2 · Anton · Chalk
│  │  It's a pause.          │    │
│  │                         │    │
│  │  The gym hasn't changed.│    │     Body · Inter · Chalk 80%
│  │  Your locker's still    │    │
│  │  empty.                 │    │
│  │                         │    │
│  │  One class is all it    │    │
│  │  takes to restart.      │    │
│  │                         │    │
│  └─────────────────────────┘    │
│                                 │
├─────────────────────────────────┤  ← ~200vh mark
│                                 │
│  ████████████████████████████    │  ← Canvas: three Volt light
│  ██ Three proof columns   ██    │     columns rising from dark
│  ██ rising from dark      ██    │
│  ████████████████████████████    │
│                                 │
│  ┌─────────────────────────┐    │  ← DOM overlay §3 (50-75% scroll)
│  │                         │    │
│  │  ┃ Marta's Tuesday      │    │     Beat 1 · Anton · Volt
│  │  ┃ Strength class has   │    │
│  │  ┃ your name on it.     │    │
│  │                         │    │
│  │  ┃ 9am or 12pm —        │    │     Beat 2 · Anton · Volt
│  │  ┃ before work, or on   │    │
│  │  ┃ your lunch break.    │    │
│  │                         │    │
│  │  ┃ Members who skip the │    │     Beat 3 · Anton · Volt
│  │  ┃ second class are 2×  │    │     (proven stat from CSV)
│  │  ┃ more likely to quit. │    │
│  │                         │    │
│  └─────────────────────────┘    │
│                                 │
├─────────────────────────────────┤  ← ~300vh mark
│                                 │
│  ████████████████████████████    │  ← Canvas: Volt-green pool of
│  ██ Pool of Volt light,    ██    │     light, booking card floating
│  ██ booking card floating  ██    │     above it
│  ██ pulse racing           ██    │
│  ████████████████████████████    │
│                                 │
│  ┌─────────────────────────┐    │  ← DOM overlay §4 (75-100% scroll)
│  │  ┌───────────────────┐  │    │
│  │  │  COMEBACK CLASS   │  │    │     Card header · Anton · Carbon
│  │  │                   │  │    │     on Volt-tinted card bg
│  │  │  Class: [Strength]│  │    │     Pre-filled select
│  │  │  With:  [Marta ▼] │  │    │     Pre-filled select
│  │  │  Day:   [Tuesday] │  │    │     Pre-filled select
│  │  │  Time:  [09:00 ▼] │  │    │     Pre-filled select
│  │  │                   │  │    │
│  │  │  First name:      │  │    │     Text input
│  │  │  [______________] │  │    │
│  │  │                   │  │    │
│  │  │  Email or phone:  │  │    │     Text input
│  │  │  [______________] │  │    │
│  │  │                   │  │    │
│  │  │  [ ] Send me a    │  │    │     UNCHECKED consent
│  │  │  reminder. I can  │  │    │     checkbox
│  │  │  opt out anytime. │  │    │
│  │  │                   │  │    │
│  │  │ ┌───────────────┐ │  │    │
│  │  │ │ BOOK MY COME- │ │  │    │     Submit · Ember bg
│  │  │ │ BACK CLASS    │ │  │    │     Carbon text (bold)
│  │  │ └───────────────┘ │  │    │
│  │  │                   │  │    │
│  │  │ No commitment.    │  │    │     Caption · Inter · Chalk 60%
│  │  │ Cancel anytime.   │  │    │
│  │  └───────────────────┘  │    │
│  └─────────────────────────┘    │
│                                 │
├─────────────────────────────────┤  ← ~400vh mark (or triggered on submit)
│                                 │
│  ┌─────────────────────────┐    │  ← DOM overlay §5 (success state)
│  │                         │    │
│  │  ██ Card pulses,        │    │     Canvas: card pulses,
│  │  ██ Volt glow peaks,    │    │     heartbeat races to full
│  │  ██ heartbeat races     │    │
│  │                         │    │
│  │  You're booked. 💪      │    │     H2 · Anton · Chalk
│  │                         │    │
│  │  Marta will see you     │    │     Body · Inter · Chalk
│  │  Tuesday at 9am.        │    │
│  │                         │    │
│  │  We texted you a        │    │
│  │  reminder.              │    │
│  │                         │    │
│  │  [Add to calendar]      │    │     Secondary CTA · Volt outline
│  │                         │    │
│  │  Want to change?        │    │     Caption · Inter · Chalk 60%
│  │  Email us anytime.      │    │
│  │                         │    │
│  └─────────────────────────┘    │
│                                 │
└─────────────────────────────────┘
```

### Section Summary Table

| Section | Scroll Range | 3D Stage | DOM Role | Primary Action |
|---------|-------------|----------|----------|----------------|
| §1 Hero | 0–25% | Resting pulse | Headline + CTA | Tap "Book your comeback class" |
| §2 Momentum | 25–50% | Gym fly-through | Reframing lines | Scroll → read → continue |
| §3 Proof | 50–75% | Light columns | Three "why now" beats | Scroll → internalise → decide |
| §4 Card | 75–100% | Volt pool + card | Booking form | Fill form → submit |
| §5 Success | Triggered on submit | Card pulse + race | Confirmation | Add to calendar |

---

## 2. The 3D Scene Concept — Four Scroll Stages

### General Scene Setup

```
Scene graph:
├── ambientLight (intensity: 0.15, color: #1a1a2e)  ← very dim fill
├── directionalLight (intensity: 0.4, color: #C6F24E, position: [5, 8, 3])
│   └── casts soft light across gym elements
├── pointLight (intensity: 0→2.5 scroll-driven, color: #C6F24E)
│   └── orbits the pulse-line origin, grows with scroll
├── pulseGroup (parent of pulse line geometry)
│   ├── pulseLine (BufferGeometry line, Volt material)
│   └── pulsePoint (small sphere, emissive Volt, travels along line)
├── gymGroup (InstancedMesh — rig, lockers, floor)
│   ├── rigPillars (BoxGeometry instanced, ~12 instances)
│   ├── floorTiles (PlaneGeometry instanced, ~30 instances)
│   └── wallPanels (BoxGeometry instanced, ~8 instances)
├── particlesGroup (Points — chalk dust)
│   └── chalkDust (BufferGeometry, ~400 points, Volt-tinted white)
├── proofGroup (three vertical light columns)
│   ├── column1, column2, column3 (CylinderGeometry, emissive Volt,
│   │   y-scale: 0→1 driven by individual scroll thresholds)
└── cardGroup (the comeback card + light pool)
    ├── voltPool (PlaneGeometry disc, emissive Volt, opacity 0.6)
    ├── cardPlane (BoxGeometry, dark with Volt edge glow)
    └── cardParticles (ring of particles orbiting the card, ~80 points)
```

### Stage 1: The Resting Pulse (scroll 0.00–0.25)

**Concept:** Total darkness except one thin Volt-green line tracing a slow heartbeat pattern. A small glowing point travels along it at ~40bpm. Nothing else visible. The visual says: "something is still alive here."

**Technical:**
- `pulseLine`: A path defined by ~8 control points forming a gentle sine-wave-like undulation across the screen (x: -3 to +3, y: +1 to -1, z: 0). Drawn as a `Line` with `LineBasicMaterial({ color: 0xC6F24E, linewidth: 2 })`.
- `pulsePoint`: A `SphereGeometry(0.08, 8, 8)` with `MeshBasicMaterial({ color: 0xC6F24E })`. Position sampled along the path using `curve.getPointAt(t)` where `t` loops 0→1 every 1.5 seconds (40bpm = one beat per 1.5s).
- `pointLight`: Intensity 0.2, colour Volt, positioned at pulsePoint.
- Camera: Static, positioned at [0, 0, 8], looking at [0, 0, 0].
- Background: Carbon `#0E1116` (scene background + `renderer.setClearColor`).
- All other groups: `visible = false` or positioned off-screen (y: -20).

**Pulse animation detail:**
- `t = (performance.now() * 0.001 % 1.5) / 1.5` — loops every 1.5s.
- At `t = 0.0`: point at left of path, dim.
- At `t = 0.5`: point at centre of path (the "beat"), brightness peaks (pointLight intensity spikes to 1.5, pulsePoint scale briefly to 1.3×).
- At `t = 1.0`: point at right, dim again.
- Between beats, the trail fades (accomplished by pointLight intensity decaying to 0.2).

**What the member sees:** A single line of green light pulsing slowly in the dark. The headline "Your spot's still here." overlaid. The CTA button glowing below.

---

### Stage 2: Momentum Builds (scroll 0.25–0.50)

**Concept:** The camera begins moving forward through a stylised FlexHaus space. The gym emerges from darkness: simple geometric shapes suggesting the rig, the studio floor, wall panels. Chalk-dust particles catch the light and swirl gently. The pulse accelerates from 40bpm → 90bpm. The visual says: "the energy is building, this place is alive."

**Technical:**
- **Camera path:** Moves along a smooth spline from `[0, 0, 8]` to `[0, -0.5, 4]` over scroll 0.25→0.50. lookAt transitions from `[0, 0, 0]` to `[0, -0.3, -2]`. Use `THREE.CatmullRomCurve3` with lerp factor 0.1.
- **gymGroup:** Fades in (opacity 0→0.8 via material opacity, or simply moves into view from below).
  - **rigPillars**: 12 vertical boxes (0.15 × 3.0 × 0.15) arranged in two parallel rows (like a rig structure). InstancedMesh, single draw call. Carbon-dark material with subtle Volt edge highlights (emissive: 0x0E1116 + 0.05 Volt).
  - **floorTiles**: 30 flat planes (1.0 × 1.0) arranged in a 6×5 grid, slight elevation variations (±0.02). InstancedMesh. Dark grey with subtle reflection hint.
  - **wallPanels**: 8 large flat boxes (3.0 × 2.0 × 0.05) positioned as studio walls, one side with a slightly lighter material suggesting mirrors. InstancedMesh.
- **chalkDust:** 400 `Points` with `PointsMaterial({ size: 0.03, color: 0xC6F24E, transparent: true, opacity: 0.5, blending: THREE.AdditiveBlending })`. Particles drift slowly (velocity ~0.02 units/s in random directions, reset when out of bounds). Emission rate increases with scroll.
- **pulseLine:** Now visible in the upper portion of the scene. Path shrinks slightly (more compressed waveform). Speed increases: 40→90bpm (beat interval: 1.5s → 0.67s).
- **pointLight:** Intensity 0.5→1.2 over this scroll range. Position drifts slightly to illuminate gym elements.

**What the member sees:** The dark gym taking shape around them as the camera glides forward. Particles of light drifting like chalk dust in sunbeams. The heartbeat line quickening. DOM overlay shows the three reframing lines.

---

### Stage 3: The Proof (scroll 0.50–0.75)

**Concept:** The camera pauses. Three vertical columns of Volt-green light rise from the darkness, one after another as the member scrolls. Each column reveals a proof beat. The pulse continues accelerating 90→120bpm. The visual says: "here are three reasons this moment matters."

**Technical:**
- **Camera:** Slows and settles at `[0, -1.0, 3.5]`, looking slightly up at the columns. Camera movement damped heavily (lerp factor 0.05) over this range.
- **proofGroup columns:**
  - Three `CylinderGeometry(0.15, 0.15, 4.0, 16)` with `MeshStandardMaterial({ color: 0x0E1116, emissive: 0xC6F24E, emissiveIntensity: 0→1.5, roughness: 0.4, metalness: 0.1 })`.
  - Positioned at x: -2.5, 0, +2.5; z: -1.0.
  - Each column's `scale.y` animates from 0→1 based on its own scroll threshold:
    - Column 1 (Marta's class): triggers at scroll 0.52, reaches full height by 0.58
    - Column 2 (9am/12pm): triggers at scroll 0.60, reaches full height by 0.66
    - Column 3 (2× stat): triggers at scroll 0.68, reaches full height by 0.74
  - Each column's `emissiveIntensity` also ramps 0→1.5 with its scale.
- **gymGroup:** Remains visible but dims slightly (ambient light decreases 0.15→0.10).
- **pulseLine:** Speed increases 90→120bpm (beat interval: 0.67s → 0.5s). Path compresses further — the waveform is tighter, more urgent.
- **DOM overlay:** Each proof beat text appears in sync with its column. The 2× stat (beat 3) is the capstone — the honest number from the CSV.

**What the member sees:** Three columns of light rising in sequence, each carrying a reason to act. The heartbeat is noticeably faster now — the page is building toward the booking moment.

---

### Stage 4: The Comeback Card (scroll 0.75–1.00)

**Concept:** The camera settles into its final position. A pool of Volt-green light spreads across the "floor" of the 3D space. The booking card floats above it, gently bobbing. The pulse races at 120→140bpm. The visual says: "this is your moment. One tap."

**Technical:**
- **Camera:** Settles at `[0, -1.5, 2.5]`, looking directly at the card position `[0, 0.2, 0]`. Final lerp factor 0.08.
- **voltPool:** A `PlaneGeometry(3.0, 2.0)` disc on the "floor" at y: -2.0, rotated to face upward. `MeshBasicMaterial({ color: 0xC6F24E, transparent: true, opacity: 0.4 })`. Fades in 0→0.4 over scroll 0.75→0.85. A second, smaller disc (r: 1.0) at opacity 0.6 for the brighter centre.
- **cardPlane:** Not rendered in 3D — the actual booking card is a DOM element. The 3D scene provides only the ambient light pool and floating particles that frame the DOM card. This avoids duplicating form UI in WebGL and keeps the form fully accessible.
  - **However**, a simple dark rectangle (1.8 × 2.4) with Volt edge glow can float in 3D space as a visual anchor, positioned at `[0, 0.2, 0]`. The DOM card overlays this position exactly.
- **cardParticles:** 80 `Points` orbiting the card position in a loose torus (radius 1.5, vertical oscillation ±0.3). `PointsMaterial({ color: 0xC6F24E, size: 0.02, blending: THREE.AdditiveBlending })`. Rotation speed: 0.3 rad/s.
- **pulseLine:** Speed increases 120→140bpm. Path is a tight, rapid oscillation — visually tense but not anxious. Positioned above the card.
- **pointLight:** Intensity peaks at 2.5. Positioned directly above the card, casting a Volt glow downward.

**What the member sees:** The Volt light pool spreading below the booking card. The card waiting, pre-filled with Strength / Marta / Tuesday / 9am. The heartbeat racing — the page is at full energy, urging the tap.

---

### Stage 5: Success State (triggered on form submit, not scroll)

**Concept:** The card pulses (scale oscillation), the Volt pool brightens intensely, the heartbeat line races to full sprint (~180bpm), chalk particles burst upward in a celebratory shower. The visual says: "you did it. You're back."

**Technical:**
- **cardPlane (3D anchor):** Scale pulses at 2Hz: oscillates between 1.0× and 1.05× over 0.5s, using `Math.sin(time * Math.PI * 4) * 0.025 + 1.025`. Continues for 3 seconds, then settles.
- **voltPool:** Opacity ramps 0.4→0.8 over 1s. Inner disc opacity 0.6→1.0.
- **pulseLine:** Speed races to 180bpm (beat interval: 0.33s). Path becomes a rapid, compressed sine — visually a blur of Volt light.
- **chalkDust:** 200 additional particles spawn and burst upward (velocity +y: 0.5→1.5, random x/z spread). Existing particles accelerate briefly. Total particles peaks at ~600 then particles decay (lifetime: 3s).
- **pointLight:** Intensity spikes to 3.5 over 0.5s, then decays to 2.0 over 2s.
- **Camera:** Subtle pull-back (z: +0.5) to frame the burst, then returns.
- **DOM overlay:** Success message fades in. "Add to calendar" button appears. Auto-scroll to success section.

---

## 3. Scroll Choreography Spec

### Scroll Progress → Scene Parameter Mapping

All values use **smooth lerp/damp** (factor 0.08–0.12) to avoid jank. Target values are computed from raw scroll progress; actual values approach targets via lerp each frame.

```
RAW SCROLL PROGRESS  →  s = window.scrollY / (documentHeight - viewportHeight)
                            clamped [0.0, 1.0]

STAGE THRESHOLDS:
  Stage 1 (Rest):     s = 0.00 – 0.25
  Stage 2 (Momentum): s = 0.25 – 0.50
  Stage 3 (Proof):    s = 0.50 – 0.75
  Stage 4 (Card):     s = 0.75 – 1.00
  Stage 5 (Success):  triggered by form submit, not scroll
```

### Parameter Table

| Parameter | s=0.00 | s=0.25 | s=0.50 | s=0.75 | s=1.00 | Success |
|-----------|--------|--------|--------|--------|--------|---------|
| **Pulse BPM** | 40 | 40 | 90 | 120 | 140 | 180 |
| **Beat interval (s)** | 1.500 | 1.500 | 0.667 | 0.500 | 0.429 | 0.333 |
| **Camera pos (z)** | 8.0 | 8.0 | 4.0 | 3.5 | 2.5 | 3.0 |
| **Camera pos (y)** | 0.0 | 0.0 | -0.5 | -1.0 | -1.5 | -1.5 |
| **Camera lookAt (z)** | 0.0 | 0.0 | -2.0 | -1.0 | 0.0 | 0.0 |
| **Camera lookAt (y)** | 0.0 | 0.0 | -0.3 | 0.0 | 0.2 | 0.2 |
| **pointLight intensity** | 0.2 | 0.2 | 1.2 | 1.8 | 2.5 | 3.5→2.0 |
| **gymGroup opacity** | 0.0 | 0.0 | 0.8 | 0.7 | 0.6 | 0.6 |
| **chalkDust count** | 0 | 150 | 400 | 400 | 400 | 600→400 |
| **chalkDust velocity** | 0.00 | 0.01 | 0.02 | 0.02 | 0.02 | 0.08→0.02 |
| **proof columns scale.y** | 0.0 | 0.0 | 0.0→1.0 | 1.0 | 1.0 | 1.0 |
| **voltPool opacity** | 0.0 | 0.0 | 0.0 | 0.0→0.4 | 0.4 | 0.8 |
| **cardParticles active** | false | false | false | true | true | true |
| **DOM §1 opacity** | 1.0→0.0 | 0.0 | 0.0 | 0.0 | 0.0 | 0.0 |
| **DOM §2 opacity** | 0.0 | 0.0→1.0→0.0 | 0.0 | 0.0 | 0.0 | 0.0 |
| **DOM §3 opacity** | 0.0 | 0.0 | 0.0→1.0→0.0 | 0.0 | 0.0 | 0.0 |
| **DOM §4 opacity** | 0.0 | 0.0 | 0.0 | 0.0→1.0 | 1.0 | 0.0 |
| **DOM §5 opacity** | 0.0 | 0.0 | 0.0 | 0.0 | 0.0 | 0.0→1.0 |

### DOM Section Crossfade Logic

Each DOM section has a "active window" of scroll progress:

```
§1 Hero:    visible when s < 0.20,  fades out [0.18, 0.22]
§2 Momentum: visible when s in [0.22, 0.48], fades in [0.22, 0.27], fades out [0.44, 0.48]
§3 Proof:   visible when s in [0.47, 0.73], fades in [0.47, 0.52], fades out [0.69, 0.73]
§4 Card:    visible when s > 0.72,   fades in [0.72, 0.78]
§5 Success: visible only when `successState === true` (triggered by form submit)
```

Transitions use `opacity` with 200ms CSS transition (not scroll-driven). When a section enters its window, its `opacity` transitions from 0 to 1; when leaving, 1 to 0. Pointer events are disabled (`pointer-events: none`) when opacity < 0.5 to prevent ghost clicks.

### Lerp Implementation Note for Kojo

```javascript
// In the animation loop (called every rAF):
const targetBPM = mapScrollToBPM(rawScrollProgress);
currentBPM += (targetBPM - currentBPM) * dampFactor; // dampFactor = 0.1

const targetCameraZ = mapScrollToCameraZ(rawScrollProgress);
camera.position.z += (targetCameraZ - camera.position.z) * dampFactor;
// ... same for all lerped parameters
```

`dampFactor` should be `1 - Math.exp(-deltaTime * smoothingConstant)` where `smoothingConstant` is ~4.0–6.0, or simply use a fixed 0.08–0.12 per frame at 60fps.

---

## 4. Component Specs

### 4.1 Hero Component (§1)

```
┌──────────────────────────────────────┐
│  TAG: <section>                      │
│  aria-label: "Comeback hero"         │
│                                      │
│  <h1>                                │
│    Your spot's still here.           │  ← Anton, Chalk, clamp(2.5rem,8vw,4.5rem)
│  </h1>                               │     line-height: 1.05, letter-spacing: -0.01em
│                                      │
│  <p>                                 │
│    No guilt. No catch-up. Just the   │  ← Inter, Chalk at 80% opacity,
│    class you loved, waiting for you. │     clamp(1rem,2.5vw,1.125rem)
│  </p>                                │     max-width: 28ch
│                                      │
│  <a href="#comeback-card">           │  ← Scroll-to CTA (smooth scroll)
│    <button>                          │     OR page anchor jump
│      Book your comeback class        │  ← Anton or Inter bold,
│    </button>                         │     clamp(1rem,3vw,1.25rem)
│  </a>                                │     Volt bg (#C6F24E),
│                                      │     Carbon text (#0E1116),
│  <p>                                 │     padding: 0.75rem 2rem,
│    ↓ Scroll or tap                   │     border-radius: 2px (sharp, athletic)
│  </p>                                │     no border, no shadow
│                                      │     hover: bg brightens to #D4F760
└──────────────────────────────────────┘
```

**Typography detail:**
- H1: `font-family: 'Anton', Impact, sans-serif; font-weight: 400; text-transform: uppercase; color: #F5F5F2;`
- Body: `font-family: 'Inter', sans-serif; font-weight: 400; color: rgba(245, 245, 242, 0.80);`
- CTA: `font-family: 'Anton', Impact, sans-serif; font-weight: 400; text-transform: uppercase; background: #C6F24E; color: #0E1116; border: none; cursor: pointer;`

**Interaction states:**
- Default: As shown.
- Hover (desktop): Volt bg shifts to `#D4F760` (10% lighter). Subtle scale 1.02 transform (150ms ease).
- Focus: 3px solid Volt outline (`outline: 3px solid #C6F24E; outline-offset: 2px;`). Visible on Carbon background.
- Active (press): Scale 0.98. Volt bg shifts to `#B0D830` (darker).
- Disabled: Not applicable for hero CTA — always active.

**Accessibility:**
- H1 is the page's primary heading. Screen reader reads: "Your spot's still here. Heading level one."
- CTA is a `<button>` inside an `<a>` (OR just an `<a>` styled as a button with `role="button"` — pick one, be consistent). Announcement: "Book your comeback class, button."

---

### 4.2 Proof Beats Component (§3)

```
┌──────────────────────────────────────┐
│  <section aria-label="Why come back">│
│                                      │
│  ┌────────────────────────────────┐  │
│  │ ┃  ← Volt vertical bar (CSS)   │  │
│  │ Marta's Tuesday Strength       │  │  ← Anton, Volt, clamp(1.25rem,3.5vw,1.75rem)
│  │ class has your name on it.     │  │     max-width: 24ch
│  └────────────────────────────────┘  │
│                                      │
│  ┌────────────────────────────────┐  │
│  │ ┃                              │  │
│  │ 9am or 12pm — before work,     │  │
│  │ or on your lunch break.        │  │
│  └────────────────────────────────┘  │
│                                      │
│  ┌────────────────────────────────┐  │
│  │ ┃                              │  │
│  │ Members who skip the second    │  │
│  │ class are 2× more likely       │  │  ← Anton, Ember (#FF5A3C) for the "2×"
│  │ to quit.                       │  │     stat emphasis. Body: Chalk.
│  └────────────────────────────────┘  │
│                                      │
└──────────────────────────────────────┘
```

**Beat visual treatment:**
- Each beat is a `<div>` with a Volt-coloured left border (3px solid `#C6F24E`) acting as the "light column" from the 3D scene.
- Text: Anton for the headline line, Inter for any supporting sentence (if added).
- Beat 3 (the stat): The "2×" is styled in Ember (`#FF5A3C`) to draw attention. The rest of the line is Chalk.
- Each beat has `opacity: 0` by default, transitions to `opacity: 1` via Intersection Observer (or scroll-driven CSS animation tied to scroll timeline). Stagger entrance: beat 2 delays 150ms after beat 1, beat 3 delays 150ms after beat 2.
- Reduced motion: Beats are always visible (no animation).

**The stat sourcing:**
- The text "Members who skip the second class are 2× more likely to quit" is the ONLY data claim on the page.
- It is sourced from Tara §2.2: 75.3% cancel rate for 1-booking members vs. 38.1% for 5+ bookings = 1.98× lift, rounded honestly to 2×.
- Kojo: add an HTML comment `<!-- Source: flexhaus_bookings.csv, 75.3% vs 38.1% cancel rate, 1.98x lift -->` near this element for cold-verify traceability.

---

### 4.3 Comeback Card / Booking Form Component (§4)

```
┌──────────────────────────────────────────┐
│  <section id="comeback-card"             │
│           aria-label="Book your          │
│           comeback class">               │
│                                          │
│  ┌────────────────────────────────────┐  │
│  │  <form id="comeback-form"          │  │
│  │        action="FORMSPREE_ENDPOINT" │  │  ← See §4.4 for endpoint spec
│  │        method="POST"               │  │
│  │        novalidate>                 │  │  ← Custom JS validation
│  │                                    │  │
│  │  <h2>COMEBACK CLASS</h2>           │  │  ← Anton, Carbon on card bg
│  │                                    │  │
│  │  ┌─ Class ──────────────────────┐  │  │
│  │  │ <label for="class-type">     │  │  │  ← Inter, Carbon, 0.875rem
│  │  │   Class                      │  │  │
│  │  │ </label>                     │  │  │
│  │  │ <select id="class-type"      │  │  │
│  │  │         name="class_type"    │  │  │
│  │  │         required>            │  │  │
│  │  │   <option>Strength</option>  │  │  │  ← PRE-SELECTED (first option)
│  │  │   <option>Boxing</option>    │  │  │
│  │  │   <option>HIIT</option>      │  │  │
│  │  │   <option>Spin</option>      │  │  │
│  │  │   <option>Yoga</option>      │  │  │
│  │  │   <option>Pilates</option>   │  │  │
│  │  │   <option>Mobility</option>  │  │  │
│  │  │ </select>                    │  │  │
│  │  └──────────────────────────────┘  │  │
│  │                                    │  │
│  │  ┌─ With ───────────────────────┐  │  │
│  │  │ <label for="instructor">     │  │  │
│  │  │   Instructor                 │  │  │
│  │  │ </label>                     │  │  │
│  │  │ <select id="instructor"      │  │  │
│  │  │         name="instructor"    │  │  │
│  │  │         required>            │  │  │
│  │  │   <option>Marta</option>     │  │  │  ← PRE-SELECTED
│  │  │   <option>Conor</option>     │  │  │
│  │  │   <option>Dev</option>       │  │  │
│  │  │   <option>Niall</option>     │  │  │
│  │  │   <option>Tom</option>       │  │  │
│  │  │   <option>Aisling</option>   │  │  │
│  │  │   <option>Saoirse</option>   │  │  │
│  │  │ </select>                    │  │  │
│  │  └──────────────────────────────┘  │  │
│  │                                    │  │
│  │  ┌─ Day ────────────────────────┐  │  │
│  │  │ <label for="day">Day</label> │  │  │
│  │  │ <select id="day"             │  │  │
│  │  │         name="day" required> │  │  │
│  │  │   <option>Tuesday</option>   │  │  │  ← PRE-SELECTED
│  │  │   <option>Monday</option>    │  │  │
│  │  │   <option>Wednesday</option> │  │  │
│  │  │   <option>Thursday</option>  │  │  │
│  │  │   <option>Friday</option>    │  │  │
│  │  │   <option>Saturday</option>  │  │  │
│  │  │   <option>Sunday</option>    │  │  │
│  │  │ </select>                    │  │  │
│  │  └──────────────────────────────┘  │  │
│  │                                    │  │
│  │  ┌─ Time ───────────────────────┐  │  │
│  │  │ <label for="time">Time</label>│  │  │
│  │  │ <select id="time"            │  │  │
│  │  │         name="time" required>│  │  │
│  │  │   <option>09:00</option>     │  │  │  ← PRE-SELECTED (default best)
│  │  │   <option>12:00</option>     │  │  │  ← Second off-peak option
│  │  │ </select>                    │  │  │
│  │  └──────────────────────────────┘  │  │
│  │                                    │  │
│  │  ┌─ First name ─────────────────┐  │  │
│  │  │ <label for="name">           │  │  │
│  │  │   First name                 │  │  │
│  │  │ </label>                     │  │  │
│  │  │ <input type="text"           │  │  │
│  │  │        id="name"             │  │  │
│  │  │        name="name"           │  │  │
│  │  │        autocomplete="given-name"│  │
│  │  │        required>             │  │  │
│  │  └──────────────────────────────┘  │  │
│  │                                    │  │
│  │  ┌─ Email or phone ─────────────┐  │  │
│  │  │ <label for="contact">        │  │  │
│  │  │   Email or phone number      │  │  │
│  │  │ </label>                     │  │  │
│  │  │ <span>So we can send you     │  │  │  ← Help text, Inter, 0.75rem
│  │  │ a reminder</span>            │  │  │     Carbon 60% opacity
│  │  │ <input type="text"           │  │  │  ← type="text" not "email"
│  │  │        id="contact"          │  │  │     (accepts phone numbers)
│  │  │        name="contact"        │  │  │
│  │  │        autocomplete="email"  │  │  │
│  │  │        inputmode="email"     │  │  │  ← Hints email keyboard
│  │  │        required>             │  │  │     on mobile but allows
│  │  └──────────────────────────────┘  │  │     phone numbers
│  │                                    │  │
│  │  ┌─ Consent ────────────────────┐  │  │
│  │  │ <input type="checkbox"       │  │  │  ← UNCHECKED by default
│  │  │        id="consent"          │  │  │     NO pre-tick
│  │  │        name="consent"        │  │  │
│  │  │        required>             │  │  │
│  │  │ <label for="consent">        │  │  │
│  │  │   Yes, send me a reminder    │  │  │  ← Plain English, GDPR-clean
│  │  │   about this class. I can    │  │  │     Inter, 0.8125rem, Carbon
│  │  │   opt out anytime.           │  │  │
│  │  │ </label>                     │  │  │
│  │  └──────────────────────────────┘  │  │
│  │                                    │  │
│  │  ┌─ Submit ─────────────────────┐  │  │
│  │  │ <button type="submit">       │  │  │
│  │  │   Book My Comeback Class     │  │  │  ← Ember bg (#FF5A3C)
│  │  │ </button>                    │  │  │     Carbon text (#0E1116)
│  │  └──────────────────────────────┘  │  │     Anton/Inter bold
│  │                                    │  │     full-width on mobile
│  │  <p>                               │  │     border-radius: 2px
│  │    No commitment. Cancel anytime.   │  │  ← Caption, Inter, 0.75rem,
│  │  </p>                              │  │     Carbon 60% opacity,
│  │                                    │  │     centred
│  │  </form>                           │  │
│  └────────────────────────────────────┘  │
│                                          │
└──────────────────────────────────────────┘
```

**Card visual styling:**
- Card container: `background: rgba(245, 245, 242, 0.95)` (Chalk at 95% opacity — near-opaque for readability). `border: 1px solid #C6F24E` (Volt hairline border). `border-radius: 4px`. `padding: 1.5rem`. `max-width: 360px`. `margin: 0 auto`. Subtle `box-shadow: 0 0 30px rgba(198, 242, 78, 0.15)` for the Volt pool glow feel (CSS only, no GPU cost).
- Form field styling:
  - Labels: `font-family: 'Inter', sans-serif; font-size: 0.875rem; font-weight: 600; color: #0E1116; display: block; margin-bottom: 0.25rem;`
  - Selects/Inputs: `width: 100%; padding: 0.625rem 0.75rem; font-family: 'Inter', sans-serif; font-size: 1rem; color: #0E1116; background: #FFFFFF; border: 1.5px solid rgba(14, 17, 22, 0.15); border-radius: 2px;`
  - Focus: `border-color: #C6F24E; outline: none; box-shadow: 0 0 0 3px rgba(198, 242, 78, 0.3);`
  - Error: `border-color: #FF5A3C; box-shadow: 0 0 0 3px rgba(255, 90, 60, 0.2);`
- Consent checkbox: Standard `<input type="checkbox">` styled with `accent-color: #C6F24E`. Label inline, not wrapped — clickable label area. `display: flex; align-items: flex-start; gap: 0.5rem;`
- Submit button: `width: 100%; padding: 0.875rem; background: #FF5A3C; color: #0E1116; border: none; font-family: 'Inter', sans-serif; font-weight: 700; font-size: 1.0625rem; text-transform: uppercase; letter-spacing: 0.02em; cursor: pointer;`
- Below-submit caption: `text-align: center; font-size: 0.75rem; color: rgba(14, 17, 22, 0.60); margin-top: 0.75rem;`

**Contrast verification for card:**
- Carbon text on Chalk (~95% white) bg: ~17:1 ✓ (AAA)
- Ember bg (#FF5A3C) with Carbon text (#0E1116): Luminance(Ember) ≈ 0.26, Luminance(Carbon) ≈ 0.006. Ratio = (0.26 + 0.05) / (0.006 + 0.05) = 0.31 / 0.056 = **5.54:1**. ✓ Passes AA for large text (submit button text is bold and ≥14pt/18.66px equivalent — the 1.0625rem = 17px bold passes AA). ⚠ Does not pass AA for normal body text on Ember — but body text is never placed on Ember background in this design. The caption text uses Carbon 60% on Chalk: check. Carbon 60% on Chalk white at 95% ≈ mid-gray on near-white. Luminance(mid-gray #3D4248) ≈ 0.05, Luminance(near-white) ≈ 0.90. Ratio = (0.90+0.05)/(0.05+0.05) = 0.95/0.10 = **9.5:1** ✓ (AAA).

**Interaction states for form fields:**
- **Default:** Light border, white background.
- **Hover (desktop):** Border darkens slightly (`rgba(14,17,22,0.3)`).
- **Focus:** Volt border + Volt glow ring. Visible focus indicator for keyboard users.
- **Error:** Ember border + Ember glow ring. Inline error message appears below field in Ember (`#FF5A3C`, Inter, 0.8125rem).
- **Disabled:** Not used in this form — all fields are always enabled.

**Interaction states for submit button:**
- **Default:** Ember bg, Carbon text.
- **Hover:** Ember lightens to `#FF7A5C` (subtle brightening). Scale 1.01 (150ms).
- **Focus:** 3px Volt outline (`outline-offset: 2px`).
- **Active:** Ember darkens to `#E04A2C`. Scale 0.98.
- **Loading (during submission):** Button text changes to "Booking…" with a subtle CSS pulse animation. Button is `disabled` + `aria-busy="true"` during fetch. Cursor: `wait`.
- **Disabled:** `opacity: 0.6; cursor: not-allowed;` (only during submission).

**Validation rules:**
1. `name`: Required, min 2 chars. Error: "Please enter your first name."
2. `contact`: Required, min 5 chars (covers "a@b.c" and phone numbers). Error: "Please enter your email or phone number."
3. `consent`: Required, must be checked. Error: "Please tick the box to receive your reminder." (Implied: if unchecked, we can't send a reminder, so the booking has no delivery mechanism — this is the GDPR-clean opt-in.)
4. All error messages appear inline below the offending field, announced via `aria-describedby` linked to the input's `aria-describedby` attribute.
5. On validation failure, focus moves to the first invalid field.

---

### 4.4 Form Submission Spec

**Primary: Formspree (free tier)**
```
Endpoint: https://formspree.io/f/{FORM_ID}
Method: POST
Headers: Accept: application/json
Body: JSON or form-encoded
Fields:
  - class_type: string
  - instructor: string
  - day: string
  - time: string
  - name: string
  - contact: string
  - consent: "yes" | "off" (checkbox value)
  - _subject: "New Comeback Booking: {name} — {class_type} with {instructor}, {day} {time}"
```

**Fallback: mailto**
```
If Formspree endpoint is not configured (detected via missing FORM_ID in JS config):
  mailto:dervla@flexhaus.ie
  ?subject=Comeback Booking: [name] — [class_type] with [instructor], [day] [time]
  &body=Name: [name]%0D%0AContact: [contact]%0D%0AClass: [class_type]%0D%0AInstructor: [instructor]%0D%0ADay: [day]%0D%0ATime: [time]
```

**Submission flow (JS):**
1. Prevent default form submit.
2. Validate all fields (client-side).
3. If invalid: show inline errors, focus first error, stop.
4. Set button to loading state.
5. `fetch(form.action, { method: 'POST', body: new FormData(form), headers: { 'Accept': 'application/json' } })`
6. On `response.ok`: trigger success state (DOM §5 fades in, 3D success animation plays, auto-scroll to success section, `aria-live` region announces success).
7. On `!response.ok` or network error: show error notification at top of form. Keep form data intact. Button returns to default state.
8. Timeout: 10 seconds. Treat as network error.

---

### 4.5 Success State Component (§5)

```
┌──────────────────────────────────────────┐
│  <section id="comeback-success"          │
│           aria-live="assertive"          │  ← Announces to screen readers
│           aria-label="Booking confirmed">│     immediately
│                                          │
│  <h2>                                    │
│    You're booked. 💪                     │  ← Anton, Chalk
│  </h2>                                   │     clamp(1.75rem,5vw,2.5rem)
│                                          │
│  <p>                                     │
│    Marta will see you Tuesday at 9am.    │  ← Inter, Chalk
│  </p>                                    │     clamp(1rem,2.5vw,1.125rem)
│                                          │     (Dynamically populated:
│  <p>                                     │      "{instructor} will see you
│    We sent you a reminder.               │       {day} at {time}.")
│  </p>                                    │
│                                          │
│  <a href="CALENDAR_LINK">                │  ← Calendar link (see below)
│    <button>                              │
│      Add to calendar                     │  ← Secondary CTA
│    </button>                             │     Volt outline, transparent bg,
│  </a>                                    │     Chalk text
│                                          │     border: 2px solid #C6F24E
│  <p>                                     │
│    Want to change? Email us              │  ← Caption, Inter, Chalk 60%
│    anytime at dervla@flexhaus.ie         │     mailto link on email
│  </p>                                    │
│                                          │
└──────────────────────────────────────────┘
```

**Calendar link:** Generate a `.ics` file download link OR a Google Calendar URL:
```
https://calendar.google.com/calendar/render?action=TEMPLATE
  &text=FlexHaus+Comeback+Class
  &details=Strength+with+Marta
  &dates=DYNAMIC_DATE_T090000/DYNAMIC_DATE_T100000
  &location=FlexHaus+Gym
```
The dynamic date should be the next occurrence of the selected day. Kojo: compute this in JS (`nextTuesday()` or equivalent based on selected day).

---

## 5. Colour Palette & Typography Tokens

### 5.1 Colour Tokens (CSS Custom Properties)

```css
:root {
  /* === PRIMARY PALETTE === */
  --color-carbon:      #0E1116;   /* Primary dark field — backgrounds, 3D scene */
  --color-chalk:       #F5F5F2;   /* Light field — primary text on dark, card bg */
  --color-volt:        #C6F24E;   /* Electric-lime accent — energy, focus, glow */
  --color-ember:       #FF5A3C;   /* Warm secondary — comeback moment, submit CTA */

  /* === DERIVED / UTILITY === */
  --color-carbon-60:   rgba(14, 17, 22, 0.60);    /* Dimmed body text on light */
  --color-chalk-80:    rgba(245, 245, 242, 0.80); /* Dimmed body text on dark */
  --color-chalk-60:    rgba(245, 245, 242, 0.60); /* Caption text on dark */
  --color-chalk-20:    rgba(245, 245, 242, 0.20); /* Subtle borders on dark */
  --color-volt-glow:   rgba(198, 242, 78, 0.30);  /* Focus ring glow */
  --color-ember-glow:  rgba(255, 90, 60, 0.25);   /* Error ring glow */
  --color-volt-light:  #D4F760;                    /* Volt hover (10% lighter) */
  --color-ember-light: #FF7A5C;                    /* Ember hover */
  --color-ember-dark:  #E04A2C;                    /* Ember active/press */

  /* === FORM FIELD COLOURS === */
  --color-field-bg:    #FFFFFF;
  --color-field-border: rgba(14, 17, 22, 0.15);
  --color-field-border-hover: rgba(14, 17, 22, 0.30);
  --color-field-focus: #C6F24E;
  --color-field-error: #FF5A3C;
  --color-card-bg:     rgba(245, 245, 242, 0.95);
}
```

### 5.2 Typography Tokens

```css
:root {
  /* === FONT FAMILIES === */
  --font-display:  'Anton', Impact, 'Arial Narrow Bold', sans-serif;
  --font-body:     'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI',
                   Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;

  /* === FONT WEIGHTS === */
  --weight-display: 400;    /* Anton is a single-weight font */
  --weight-body:    400;
  --weight-body-bold: 600;
  --weight-cta:     700;    /* Inter Bold for CTAs if not using Anton */

  /* === TYPE SCALE (mobile-first, fluid via clamp) === */
  --text-hero:       clamp(2.5rem, 8vw, 4.5rem);
  --text-h2:         clamp(1.75rem, 5vw, 2.5rem);
  --text-beat:       clamp(1.25rem, 3.5vw, 1.75rem);
  --text-body-lg:    clamp(1rem, 2.5vw, 1.25rem);
  --text-body:       clamp(1rem, 2.5vw, 1.125rem);
  --text-cta:        clamp(1rem, 3vw, 1.25rem);
  --text-label:      0.875rem;
  --text-small:      0.8125rem;
  --text-caption:    0.75rem;

  /* === LINE HEIGHTS === */
  --leading-tight:   1.05;    /* Hero headlines */
  --leading-snug:    1.2;     /* Section headings, beats */
  --leading-normal:  1.5;     /* Body copy */
  --leading-form:    1.4;     /* Form labels and inputs */
}
```

### 5.3 AA Contrast Compliance Matrix

| Foreground | Background | Ratio | Pass Level | Usage |
|-----------|-----------|-------|------------|-------|
| Chalk `#F5F5F2` | Carbon `#0E1116` | **17.6:1** | AAA ✓ | All dark-section text |
| Carbon `#0E1116` | Volt `#C6F24E` | **9.5:1** | AAA ✓ | Hero CTA text, focus rings |
| Carbon `#0E1116` | Ember `#FF5A3C` | **5.5:1** | AA (large) ✓ | Submit button text (bold, ≥17px) |
| Volt `#C6F24E` | Carbon `#0E1116` | **9.5:1** | AAA ✓ | Beat text, glow elements |
| Ember `#FF5A3C` | Carbon `#0E1116` | **5.5:1** | AA (large) ✓ | Stat "2×" emphasis (large display text) |
| Carbon `#0E1116` | Chalk `#F5F5F2` | **17.6:1** | AAA ✓ | Card text, form labels |
| Chalk 80% | Carbon `#0E1116` | **~14:1** | AAA ✓ | Dimmed body text |
| Carbon 60% | Chalk `#F5F5F2` | **~9.5:1** | AAA ✓ | Card caption text |
| Volt `#C6F24E` | Chalk `#F5F5F2` | **1.4:1** | FAILS ✗ | ⚠ NEVER use Volt text on Chalk bg |
| Chalk `#F5F5F2` | Ember `#FF5A3C` | **3.2:1** | FAILS ✗ | ⚠ NEVER use Chalk text on Ember bg |

**⚠ TRADE-OFF FLAGGED — Ember + Chalk contrast:**
The Ember (#FF5A3C) + Chalk (#F5F5F2) combination fails AA (3.2:1). This design avoids it entirely: the submit button uses CARBON text on Ember background (5.5:1, passes AA for large text). Do not place Chalk-coloured text on Ember-coloured backgrounds anywhere. If a future design element needs text on Ember, use Carbon text.

**⚠ TRADE-OFF FLAGGED — Volt + Chalk contrast:**
Volt (#C6F24E) + Chalk (#F5F5F2) fails AA (1.4:1). Volt text may only appear on Carbon background. Volt background may only carry Carbon text. Never put Volt and Chalk adjacent as text/background. This constraint is satisfied in the current design — proof beats use Volt text on Carbon, CTA uses Carbon text on Volt.

---

## 6. Reduced-Motion Fallback Design

**Trigger:** `window.matchMedia('(prefers-reduced-motion: reduce)').matches === true`

### Behaviour Changes

1. **Canvas:** Not rendered. The `<canvas>` element is hidden (`display: none`). All 3D animation is disabled. No GPU resources consumed.
2. **Hero background:** A static CSS gradient replaces the 3D pulse:
   ```css
   background: radial-gradient(ellipse at 50% 40%, #1a2a0a 0%, #0E1116 70%);
   ```
   A subtle Volt-tinted radial glow from centre-top. No animation.
3. **Hero headline:** Identical to the 3D version — "Your spot's still here." Same typography, same CTA.
4. **Sections §2 and §3:** Rendered as standard scrolling sections with Carbon background. No scroll-driven opacity crossfades — all sections are stacked vertically in normal document flow.
5. **Section §2 (Momentum):** The three reframing lines are static text on Carbon background, separated by generous whitespace.
6. **Section §3 (Proof):** The three proof beats are static, with the same Volt left-border treatment. They fade in via `IntersectionObserver` (entrance animation with `opacity` and `translateY(10px) → translateY(0)`, 400ms ease — this is considered acceptable under prefers-reduced-motion because it's a simple opacity/position transition, not continuous animation. If more conservative: make them always visible with no animation).
7. **Section §4 (Card):** Identical to the 3D version. The card is a DOM element; no 3D dependency.
8. **Section §5 (Success):** Identical. Static celebration, no pulse animation.
9. **Scroll behaviour:** Normal browser scrolling. No lerp, no damp, no scroll-jacking.
10. **Page structure:** All sections occupy normal document flow. Total page height: determined by content, not forced to 400vh.

### User Experience

The reduced-motion page is functionally identical — the booking form, the pre-filled defaults, the proof beats, and the CTA are all present. The only difference is the absence of 3D animation. The emotional arc is carried by typography, colour, and layout alone.

---

## 7. No-WebGL Fallback Design

**Trigger:** `try { canvas.getContext('webgl2') || canvas.getContext('webgl') } catch(e) { /* no WebGL */ }` — also triggers if WebGL context creation throws, or if the browser lacks WebGL support entirely.

### Behaviour

Identical to the **reduced-motion fallback** (§6) plus one additional element:

**Hero "pulse hint":** A single CSS-animated line replaces the 3D heartbeat:
```html
<div class="no-webgl-pulse" aria-hidden="true">
  <div class="pulse-line"></div>
</div>
```
```css
.no-webgl-pulse {
  position: absolute;
  top: 30%;
  left: 10%;
  right: 10%;
  height: 2px;
  background: rgba(198, 242, 78, 0.15);
}
.pulse-line {
  width: 3px;
  height: 3px;
  border-radius: 50%;
  background: #C6F24E;
  box-shadow: 0 0 12px #C6F24E, 0 0 24px rgba(198, 242, 78, 0.5);
  position: absolute;
  top: -1px;
  /* Brief CSS animation: travels left→right once on load, then static */
  animation: pulse-travel 2s ease-in-out 1;
}
@keyframes pulse-travel {
  0% { left: 0%; opacity: 0; }
  20% { opacity: 1; }
  50% { left: 50%; opacity: 1; transform: scale(2); }
  80% { opacity: 1; }
  100% { left: 100%; opacity: 0; }
}
```

This is a single-play CSS animation that evokes the 3D heartbeat line without WebGL. It runs once on page load and then remains static (respecting the user — no infinite animation in a fallback context, even without `prefers-reduced-motion`).

**All other sections:** Identical to reduced-motion fallback.

**Booking form:** Fully functional. No dependency on WebGL whatsoever.

**Detection and switching logic:**
```javascript
// In init():
let useWebGL = false;
try {
  const canvas = document.getElementById('scene-canvas');
  const gl = canvas.getContext('webgl2') || canvas.getContext('webgl');
  useWebGL = !!gl && !window.matchMedia('(prefers-reduced-motion: reduce)').matches;
} catch(e) {
  useWebGL = false;
}

if (useWebGL) {
  initThreeJS();
} else {
  showFallbackHero();
}
```

---

## 8. Accessibility Plan

### 8.1 Semantic Structure

```
<main>
  <section aria-label="Comeback hero">           ← §1
    <h1>Your spot's still here.</h1>
    <a href="#comeback-card">Book your comeback class</a>
  </section>

  <section aria-label="Your comeback story">     ← §2
    <p>It's not a setback. It's a pause.</p>
    <p>The gym hasn't changed. Your locker's still empty.</p>
    <p>One class is all it takes to restart.</p>
  </section>

  <section aria-label="Why come back now">       ← §3
    <p>Marta's Tuesday Strength class has your name on it.</p>
    <p>9am or 12pm — before work, or on your lunch break.</p>
    <p>Members who skip the second class are 2× more likely to quit.</p>
  </section>

  <section aria-label="Book your comeback class">← §4
    <h2>Comeback Class</h2>
    <form>...</form>
  </section>

  <section aria-live="assertive"                 ← §5 (hidden until success)
           aria-label="Booking confirmed">
    <h2>You're booked.</h2>
    ...
  </section>
</main>
```

### 8.2 Canvas Accessibility

```html
<canvas id="scene-canvas"
        aria-hidden="true"
        role="presentation">
  <!-- Fallback text for browsers that don't render canvas at all -->
  FlexHaus Gym — Book your comeback class. Strength with Marta, Tuesday at 9am.
</canvas>
```

- `aria-hidden="true"` ensures screen readers skip the canvas entirely.
- All meaningful content exists in DOM sections (headline, proofs, form, success).
- The canvas is visual decoration that supports but does not replace content.

### 8.3 Focus Order

1. Browser chrome / skip-link (if added)
2. **"Book your comeback class"** CTA button (hero) — `tabindex="0"` (natural `<a>` or `<button>`)
3. (Scrolls to form section)
4. **Class type** `<select>`
5. **Instructor** `<select>`
6. **Day** `<select>`
7. **Time** `<select>`
8. **Name** `<input>`
9. **Contact** `<input>`
10. **Consent** `<input type="checkbox">`
11. **"Book My Comeback Class"** submit `<button>`
12. (On success) **"Add to calendar"** button
13. Footer / mailto link

**Skip link (recommended, add in `<body>` immediately after opening):**
```html
<a href="#comeback-card" class="skip-link">
  Skip to booking form
</a>
```
Styled: visually hidden until focused (`:focus` reveals it as a Volt bar at top of page).

### 8.4 Focus Indicators

All interactive elements receive a visible focus ring:
```css
*:focus-visible {
  outline: 3px solid #C6F24E;
  outline-offset: 2px;
}
```

For elements on Carbon background: Volt outline is clearly visible.
For elements on Chalk/card background (form): Volt outline with `outline-offset: 2px` plus `box-shadow: 0 0 0 4px rgba(198, 242, 78, 0.25)` as double insurance.

### 8.5 Form Accessibility

- Every input has a `<label>` with a matching `for`/`id` pair.
- No placeholder-only fields (placeholders are supplementary only).
- Required fields marked with `required` attribute (browsers announce this). No asterisk — the `required` attribute + `aria-required="true"` is sufficient.
- Error messages linked via `aria-describedby`:
  ```html
  <input id="name" aria-describedby="name-error" ...>
  <span id="name-error" role="alert">Please enter your first name.</span>
  ```
- `role="alert"` on error containers ensures screen readers announce errors immediately.
- Consent checkbox has a large clickable label area (the entire text is inside the `<label>`).
- Submit button announces "Book My Comeback Class, button" to screen readers.

### 8.6 Dynamic Content Announcements

- **Form errors:** `role="alert"` on each error `<span>`. Updated on validation.
- **Submission in progress:** `aria-busy="true"` on the form during fetch. Button text changes to "Booking…"
- **Success:** The success section has `aria-live="assertive"`. When it becomes visible (display: none → block), screen readers announce its contents immediately. The first element is `<h2>You're booked.</h2>`.
- **Form reset / retry:** If the user needs to book again (unlikely in this single-booking flow but for robustness), the form resets.

### 8.7 Screen Reader Walkthrough

1. Page loads. Screen reader announces: "Your spot's still here. Heading level one. No guilt. No catch-up. Just the class you loved, waiting for you. Book your comeback class, link."
2. User navigates to link, activates it. Page scrolls.
3. As user scrolls, screen reader encounters §2: "It's not a setback. It's a pause." etc.
4. §3: "Marta's Tuesday Strength class has your name on it." etc. The stat: "Members who skip the second class are 2 times more likely to quit."
5. §4: "Comeback Class, heading level two. Class, combobox, Strength. Instructor, combobox, Marta..." — each field clearly labeled.
6. User fills form, tabs to consent checkbox: "Yes, send me a reminder about this class. I can opt out anytime. Checkbox, not checked."
7. User checks it, tabs to submit: "Book My Comeback Class, button."
8. On success: screen reader immediately interrupts with "You're booked. Heading level two. Marta will see you Tuesday at 9am." (assertive live region).
9. User can then hear "Add to calendar, link."

### 8.8 Keyboard Navigation

- All interactive elements are natively keyboard-accessible (`<a>`, `<button>`, `<select>`, `<input>`, `<input type="checkbox">`).
- No custom keyboard handlers needed.
- `Enter` or `Space` activates buttons.
- `Tab` / `Shift+Tab` moves through focus order.
- No focus traps.
- Scroll position follows focus (browser default behaviour — `scrollIntoView` on focus is automatic for form elements).

---

## 9. Interaction States (Loading, Empty, Error, Success)

### 9.1 Loading State

**Timeline:**
- t=0ms: HTML delivered. Carbon background visible. Hero DOM content (headline + CTA) visible immediately (it's in the HTML, no JS dependency).
- t=0–500ms: WebGL initialisation. Canvas is transparent over the Carbon background (no flash of white).
- t=500–1500ms: 3D scene builds (geometries, materials, lights). During this time, the DOM hero is fully interactive — the CTA button works, scrolling works (though the 3D scene won't animate until ready).
- t>2000ms: If 3D still not ready, show a subtle loading indicator: the Volt CTA button gains a subtle CSS pulse animation (`@keyframes pulse { 0%,100% { opacity:1 } 50% { opacity:0.7 } }` — 2s cycle). This indicates "something is still loading" without blocking interaction.
- t>5000ms: Give up on WebGL. Switch to no-WebGL fallback hero. The page remains fully functional.

**Loading indicator (if needed beyond 2s):**
- A thin Volt-coloured line at the very top of the viewport, 3px tall, animating width from 0% to 100% over the estimated remaining load time (indeterminate progress bar). Positioned absolutely, above all content.
- CSS only: `@keyframes loading-bar { 0% { width: 0%; margin-left: 0%; } 50% { width: 40%; margin-left: 30%; } 100% { width: 0%; margin-left: 100%; } }`

### 9.2 Empty State

Not applicable. The page always renders with content. There is no "no results" or "no data" scenario — the booking form, class list, instructor list, and time slots are all hardcoded defaults per Tara's diagnosis.

### 9.3 Error States

| Error | Trigger | Visual | Screen Reader | Recovery |
|-------|---------|--------|---------------|----------|
| **WebGL init failure** | Context creation fails | Fallback hero (§7) appears | No change — DOM content always present | Automatic; no user action needed |
| **Form validation error** | Required field empty/invalid on submit | Field border turns Ember; error text appears below field in Ember; first error field focused | Error text announced via `role="alert"` | User corrects field; error clears on input |
| **Form submission network error** | `fetch()` fails or times out | Toast notification at top of card: "Something went wrong. Please try again or email us at dervla@flexhaus.ie." Ember left-border, Carbon text on Chalk bg | Toast announced via `role="alert"` | User can retry (form data preserved) or use mailto link |
| **Form submission server error** | Formspree returns 4xx/5xx | Same as network error toast | Same | Same |
| **JavaScript disabled** | `<noscript>` or form falls back to default browser submit | The form still works — it submits via default HTML form action (POST to Formspree). No client-side validation, no 3D. Formspree handles validation on its side. | Standard browser form behaviour | Formspree redirects to its own success/error page |

**Error toast component:**
```
┌──────────────────────────────────────────┐
│ ┃ Something went wrong.                  │  ← Ember left border (3px)
│   Please try again or email us at        │     Carbon text on Chalk bg
│   dervla@flexhaus.ie                     │     Inter, 0.875rem
│                               [Dismiss]  │     Close button (×)
└──────────────────────────────────────────┘
```
- Appears at top of form card.
- `role="alert"` for screen reader announcement.
- Auto-dismisses after 8 seconds OR on manual dismiss.
- Does not clear form data.

### 9.4 Success State

Already detailed in §4.5. Key points:
- DOM §5 becomes visible (`display: block` → was `display: none`).
- `aria-live="assertive"` announces the success to screen readers.
- 3D scene plays the success animation (card pulse, particle burst, heartbeat race).
- Form §4 becomes hidden (or transitions to `display: none`).
- Auto-scroll to success section (`element.scrollIntoView({ behavior: 'smooth' })`).
- "Add to calendar" button appears with generated calendar link.
- No auto-redirect. The member stays on the success page.

---

## 10. Design Rationale — Linking Every Decision to Tara's Diagnosis

| Design Decision | Tara's Evidence | Rationale |
|-----------------|-----------------|-----------|
| **Pre-filled: Strength, Marta, Tuesday, 9am** | §5.7: Highest return rate (64.4% Marta, 62.2% Tuesday, 60.4% Strength) + acceptable no-show + off-peak capacity | Reducing cognitive load to near-zero. The member doesn't need to remember what class they liked, what time works, or who taught — it's already chosen for them based on what the data says works best. |
| **The 1.98× stat in the proof section** | §2.2: 75.3% vs 38.1% cancel rate = 1.98× lift | The only data claim on the page. It's honest (rounded to 2×), traceable to the CSV, and positions the second class as genuinely consequential — not marketing fluff. |
| **"Your spot's still here." as the headline** | §1 verbatim: "If someone had just texted me 'we saved you a spot Tuesday', I'd have gone." — F, 38, at-risk | The exact language members asked for. Not "we miss you" (generic). Not "come back" (implies they left). "Your spot's still here" — the gym held their place. |
| **No shaming, no false urgency** | §2.5 constraints: "these people feel guilty already. Shame does not sell a comeback." + verbatims showing guilt | Every line of copy is warm. "It's not a setback. It's a pause." The design never says "you've been slacking" or "don't miss out." No countdown timers. No "limited spots." |
| **One primary action: book a class** | §7: The ONE metric is comeback-class bookings | No newsletter signup, no social follow, no "tell a friend." One page, one goal, one button (the hero CTA scrolls to the form; the form has one submit). Every element either motivates or converts — nothing distracts. |
| **Mobile-first design** | §3.6: Instagram and friend-referral are the top acquisition sources — mobile-native channels. The at-risk member is on their phone. | Type scale uses `clamp()` for fluid sizing. Form is full-width. Touch targets ≥44px. No hover-dependent interactions. Canvas rendered at capped DPR. |
| **Off-peak slots (9am, 12pm) as time options** | §2.3: "Off-peak capacity sits empty" + §5.7: 9am (65.6% return) and 12pm (64.0% return) are highest off-peak return rates | The gym has empty studio space during the day. Booking a returning member into an off-peak slot costs FlexHaus nothing and fills dead capacity. It also avoids competing with the 6pm rush that waitlisted members fight for. |
| **Three proof beats structure** | §8: Three behavioural cohorts — fresh one-and-done, long-lapsed, quietly stopped regulars | Each beat speaks to a different reason for not returning, but all three lead to the same action. Beat 1 (Marta has your name) = belonging. Beat 2 (9am or 12pm) = convenience. Beat 3 (2× stat) = consequence. |
| **GDPR-clean consent — unchecked, plain English** | §2.5: "a marketing/win-back message still needs a clear purpose and an easy opt-out; no dark patterns" | "Yes, send me a reminder about this class. I can opt out anytime." — unchecked. The member must affirmatively consent. No pre-tick. No tiny grey text. The language is plain, not legalese. |
| **Scroll-driven 3D as emotional arc, not decoration** | §5: "The page is not a flat form. It is a short, kinetic, scroll-driven WebGL experience that turns a guilty lapse into an easy return." | The 3D scenes map to emotional states: stillness → momentum → conviction → action → celebration. Each stage earns its GPU cost by moving the member closer to booking. |
| **Marta as default instructor** | §5.3: Marta's 64.4% return rate is the highest — 5.6 points above the next instructor | A first class with Marta is the single strongest predictor of returning. The pre-selection leverages this. If the member's first class was with someone else, they can change the dropdown — but Marta is the safest default for conversion. |
| **Reduced-motion and no-WebGL fallbacks are first-class, not afterthoughts** | §5 technical requirements: "If any 3D ambition threatens the mobile-performance or accessibility bars, cut the ambition, not the bars." | Both fallbacks render a fully functional booking page. The form captures identically. The emotional arc is carried by typography and layout. The page converts with or without 3D. |
| **Formspree + mailto fallback for form handling** | §8 (Tara to Kojo): "No backend exists, so wire to Formspree or a documented mailto fallback." | Free tier (50 submissions/month) covers the 195-member campaign launch. mailto ensures zero-data-loss if Formspree is not configured. Dervla gets every booking. |

---

## Performance Budget & Trade-Off Flags

### ⚠ FLAG 1: Post-Processing Bloom on Mobile

**Risk:** `UnrealBloomPass` from three.js post-processing is expensive on mid-range mobile GPUs. A full-screen bloom pass at native resolution can drop a Mali-G57 or Adreno 618 below 30fps.

**Recommendation:** Do NOT use three.js post-processing bloom. Instead:
- The Volt pool glow on the 3D floor is achieved via a semi-transparent `PlaneGeometry` with `MeshBasicMaterial({ color: 0xC6F24E, transparent: true, opacity: 0.4 })` + a second smaller plane at opacity 0.6. This is CHEAP — two draw calls.
- The DOM booking card's glow is achieved via CSS `box-shadow: 0 0 40px rgba(198, 242, 78, 0.2), 0 0 80px rgba(198, 242, 78, 0.1)` — zero GPU cost.
- If bloom is absolutely desired for the Volt glow aesthetic, use a single-pass, quarter-resolution render target (render at 0.25× canvas size, then draw the result as a full-screen quad with additive blending). This is ~6% of the fill-rate cost of a full-res bloom. But even this is optional — the design works without it.

### ⚠ FLAG 2: Particle Count on Low-End Devices

**Risk:** 400 chalk-dust particles + 80 card particles at 60fps is fine on mid-range. On low-end devices (e.g., Snapdragon 450, 2GB RAM), even 400 particles may cause frame drops.

**Recommendation:** Detect device tier via `navigator.hardwareConcurrency` and/or screen size:
- High-tier (≥6 cores, ≥4GB inferred): 400 chalk + 80 card = 480 particles
- Mid-tier (4 cores): 250 chalk + 60 card = 310 particles
- Low-tier (≤3 cores or small screen): 150 chalk + 40 card = 190 particles, OR switch to no-WebGL fallback entirely

Kojo: implement a simple device tier check in init. Default to mid-tier if uncertain.

### ⚠ FLAG 3: Canvas Resolution and Device Pixel Ratio

**Risk:** Rendering at `window.devicePixelRatio` (often 3× on modern phones) means 9× the pixels of a 1× canvas. Fill rate kills performance.

**Recommendation:** Cap at `Math.min(window.devicePixelRatio, 2)`. On a 375px-wide phone at 2× DPR, the canvas is 750px wide — reasonable. At 3×, it would be 1125px — too expensive.

### ⚠ FLAG 4: three.js Payload Size

**Risk:** Full three.js build + post-processing + OrbitControls can exceed 500KB gzipped.

**Recommendation:** Use ES module imports from CDN (unpkg or jsdelivr) with import map. Import only what's needed:
```javascript
import * as THREE from 'three';
// Do NOT import OrbitControls (not needed — camera is scroll-driven)
// Do NOT import EffectComposer/UnrealBloomPass (unless using quarter-res bloom)
// Do NOT import GLTFLoader (no 3D models — all geometry is procedural)
```
Target: <150KB gzipped for the three.js core. The procedural scene (geometries, materials) adds negligible size. Total JS payload target: <200KB gzipped.

### ⚠ FLAG 5: Scroll Performance and Main Thread

**Risk:** Reading `window.scrollY` inside `requestAnimationFrame` can cause layout thrashing if combined with DOM writes.

**Recommendation:**
- Read scroll position ONCE at the top of the rAF callback.
- Compute all target values from that single reading.
- Apply all scene updates (camera, materials, positions).
- Do NOT read `getBoundingClientRect()`, `offsetHeight`, or any layout-triggering properties inside the animation loop.
- DOM section crossfades should use CSS `opacity` transitions (GPU-composited), NOT JS-driven opacity changes. Use `IntersectionObserver` to toggle CSS classes that trigger transitions.
- Use `will-change: opacity` on DOM overlay sections to promote them to their own compositor layer.

---

## Spec Summary for Kojo (Maker)

**What you're building:**
A single `index.html` file (+ inline CSS/JS, or separate files in `/site/`) that delivers:
1. A full-viewport three.js canvas with 4 scroll-driven 3D stages (pulse line → gym fly-through → proof columns → card with Volt pool)
2. DOM overlay sections (hero, momentum, proof, booking card, success) with scroll-driven crossfades
3. A working booking form pre-filled with Strength / Marta / Tuesday / 09:00
4. Form submission via Formspree (with mailto fallback)
5. Reduced-motion fallback (no 3D, static hero, normal scroll)
6. No-WebGL fallback (CSS hero with pulse hint + static sections)
7. Full accessibility (labels, focus rings, ARIA, semantic HTML)
8. Mobile-first responsive design
9. Deployed to GitHub Pages

**Files to create:**
- `/site/index.html` — the complete page
- `/site/README.md` — run + deploy instructions

**Key build constraints:**
- No build step. Vanilla HTML/CSS/JS. three.js from CDN import map.
- Single HTML file preferred (inline CSS + JS) for simple GitHub Pages deploy. If that's too large, split into `/site/css/style.css` and `/site/js/main.js`.
- The form must ACTUALLY submit — test with a real Formspree endpoint or verify mailto fallback works.
- Test on a real phone (or Chrome DevTools mobile emulation at minimum).
- Test with `prefers-reduced-motion: reduce` enabled.
- Test with WebGL disabled (Chrome: `chrome://flags/#disable-webgl`).
- Test with keyboard only (Tab through entire page).
- Test with a screen reader (NVDA on Windows or VoiceOver on Mac).

---

## Handoff

**From:** Mateo (Red-Orange Designer)
**To:** Kojo (Blue Maker)
**What was done:** Complete design spec for the FlexHaus comeback-booking page — page architecture, 4-stage 3D scene concept, scroll choreography mapping, component specs for all 5 sections, colour palette and typography tokens, reduced-motion and no-WebGL fallback designs, accessibility plan, interaction states for all scenarios, and performance budget with flagged trade-offs.
**What's needed next:** Build the page. Start with the DOM structure and form (works without 3D). Add the three.js scene layer. Test fallbacks. Deploy to GitHub Pages.
**Open questions:** None. The spec is complete. Escalate to Manager if any specification is ambiguous, if a 3D technique exceeds the performance budget on a test device, or if a design element cannot be built as described within the €0 hosting / vanilla JS constraints.
