# Round 4: lighten + restore navbar + favicon

Victor's feedback on the live page: feels sluggish / heavy / "non-functional"; wants a lighter
look and feel; restore the cleaner old navbar; add a 🐙 favicon. Keep three.js.

## Diagnosis (verified by reading the source)
- Lenis smooth scroll (inertia) + TWO pinned+scrubbed ScrollTriggers = the scroll is hijacked.
  That is the "non-functional" feeling: the user fights the scroll.
- Full-screen GLSL fragment shader every frame + ~13 lights (1 ambient, 6 point, 6 idle) = heavy GPU.
- Always-on render loop.

## Renzo direction (the bar)
Lightness, not weight, is the quality signal. Native scroll. Calm, effortless motion. Air and
crispness over density. The 3D is a quiet accent, not a heavy scene. The page should feel instant.

## What changes
PERFORMANCE / FEEL (the priority):
1. Remove Lenis. Native scroll only.
2. Remove BOTH ScrollTrigger pin+scrub. No scroll-jacking. Hero is a normal section; page scrolls
   natively and smoothly.
3. 3D core: a gentle ALWAYS-ON idle animation (slow drift/breathe), independent of scroll.
4. Slash WebGL cost: drop the 13 lights (use emissive / additive glow sprites, no scene lights or
   at most 1). Replace the full-screen fragment shader with a cheap static field (CSS gradient +
   sparse static points) or a very cheap shader. Cap DPR <= 1.5. Pause the render loop when the tab
   is hidden AND when the hero canvas scrolls out of view (IntersectionObserver). Reduce geometry detail.
5. Reveals: simple cheap fade-up via IntersectionObserver or plain CSS, never pin/scrub.

LIGHTER LOOK & FEEL (visual):
- Open it up: more whitespace, generous spacing, lighter surface panels, less oppressive black.
  Keep the dark Octopus brand but airier, crisper, calmer. Reduce visual weight/density.

NAVBAR (restore the clean style Victor likes):
- Clean "OCTOPUS" wordmark (letterspaced, subtle), DROP the 5 colored dots clutter. Horizontal
  nav links + a GitHub pill. Lighter, cleaner bar. Links must point to REAL sections that exist
  (#agents #workflow #verify #safety #start) with clean labels.

FAVICON: add a 🐙 octopus emoji favicon via inline SVG data URI in <head>.

CONSTRAINTS: single self-contained file, still three.js, 0 em dashes, WCAG AA, reduced-motion
respected, content visible by default (keep progressive enhancement). Verify by rendering + looking.

## Acceptance (binary)
R4.1 No Lenis, no ScrollTrigger pin, no scrub anywhere (grep proves it).
R4.2 Scene lights reduced to <= 1; full-screen per-frame fragment shader removed or made cheap.
R4.3 Render loop pauses on hidden tab AND when hero canvas is offscreen.
R4.4 Navbar restored to the clean wordmark + links + GitHub style; links resolve to real sections.
R4.5 🐙 favicon present in <head>.
R4.6 Lighter, airier visual feel; still three.js constellation present and idling.
R4.7 0 em dashes; content visible by default; reduced-motion path intact; no console errors.
