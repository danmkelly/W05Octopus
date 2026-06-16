# FlexHaus Retention Booking Page — Design Spec

## Handoff: Designer > Maker

---

## 1. Design Overview

### What This Solves

FlexHaus has a churn cliff between the first and second class. 42.1% of all members never book a second class; 24.8% are "one-and-done" after a single booking. The dominant predictor is behavioral (booking gap, no-show rate), not demographic. This page intercepts members at three measurable churn moments and removes every possible friction between them and a second booking.

### Design Goal

A single-page booking experience that makes re-booking feel like a welcome-back invitation — not a guilt trip. The emotional tone is "we noticed you, we miss you, here's an easy path back." Every design decision traces to a specific research insight.

### Three Entry Contexts (URL-Parameter Driven)

| Entry Code | Trigger Condition | Hero Messaging Family |
|---|---|---|
| `at-risk` | One-and-done members (booked once, 7+ days silence) | "We saved your spot, [Name]" |
| `no-show` | First-class no-show (within 24h of missed class) | "Fresh start. New class. No pressure." |
| `inactive` | Booked first class, no second booking in 7+ days | "Your next class is waiting" |

**If no entry code is present,** default to the `inactive` variant with generic messaging. The page must still function.

---

## 2. User Flow Diagram

```
                          MEMBER CLICKS LINK (SMS/Email)
                                    │
                                    ▼
                    ┌───────────────────────────────┐
                    │  URL PARAMS PARSED             │
                    │  ?entry=[at-risk|no-show|      │
                    │          inactive]              │
                    │  &name=[FirstName]              │
                    │  &memberId=[FH-XXXXX]           │
                    └──────────────┬────────────────┘
                                   │
                    ┌──────────────▼────────────────┐
                    │  STEP 1: HERO + WELCOME         │
                    │  - Personalized headline        │
                    │  - Subheadline (context-aware)  │
                    │  - Single CTA: "Pick my class"  │
                    │  [Member sees this first]       │
                    └──────────────┬────────────────┘
                                   │  tap/click CTA
                    ┌──────────────▼────────────────┐
                    │  STEP 2: CLASS TYPE SELECTOR    │
                    │  - "Recommended" badge on       │
                    │    Pilates, HIIT                │
                    │  - 4-5 class type cards         │
                    │  - Tap to select, visual confirm│
                    │  - "Continue" button appears     │
                    └──────────────┬────────────────┘
                                   │  select + continue
                    ┌──────────────▼────────────────┐
                    │  STEP 3: DATE + TIME PICKER     │
                    │  - Next 7 days visible          │
                    │  - Available slots for selected  │
                    │    class type shown              │
                    │  - Time slots as tappable chips  │
                    │  - "Continue" after selection    │
                    └──────────────┬────────────────┘
                                   │  select + continue
                    ┌──────────────▼────────────────┐
                    │  STEP 4: CONFIRM + IDENTIFIER   │
                    │  - Summary: class, date, time    │
                    │  - Name field (pre-filled if     │
                    │    URL param present)            │
                    │  - Member ID field (pre-filled)  │
                    │  - "Save My Spot" CTA (primary)  │
                    └──────────────┬────────────────┘
                                   │  submit
                    ┌──────────────▼────────────────┐
                    │  STEP 5: CONFIRMATION STATE     │
                    │  - Success animation            │
                    │  - "You're booked!" message      │
                    │  - Class details card            │
                    │  - Calendar add link             │
                    │  - "Good to know" tips section   │
                    └────────────────────────────────┘
```

**Important:** Steps 2-4 exist as scrollable sections within the same page — not separate page loads. The hero is always visible at the top. The flow uses progressive disclosure: each step scrolls into view as the previous one completes. On mobile, sections stack vertically with smooth scroll behavior.

---

## 3. Page Architecture — Section-by-Section Wireframe

### Full-Page Structure (Mobile-First)

```
┌──────────────────────────────────────┐
│                                      │  ← FlexHaus brand bar (thin, logo only)
│  [FlexHaus logo — small, centered]   │     Height: 48px
├──────────────────────────────────────┤
│                                      │
│  ┌──────────────────────────────┐    │
│  │         HERO SECTION         │    │  ← Context-aware messaging
│  │  Headline (2 lines max)      │    │     Padding: 40px 24px
│  │  Subheadline (1-2 lines)     │    │     BG: #f0ece4
│  │  [Pick My Class] CTA button  │    │
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │    CLASS TYPE SELECTOR       │    │  ← Initially collapsed or
│  │  "What kind of class?"       │    │     shown below hero
│  │  ┌─────┐ ┌─────┐ ┌─────┐   │    │     Cards: 2x2 grid (mobile)
│  │  │Pila-│ │ HIIT│ │Yoga │   │    │            4-across (desktop)
│  │  │tes  │ │     │ │     │   │    │
│  │  │ ★   │ │ ★   │ │     │   │    │  ★ = "Recommended" badge
│  │  └─────┘ └─────┘ └─────┘   │    │
│  │  ┌─────┐                    │    │
│  │  │Spin │                    │    │
│  │  └─────┘                    │    │
│  │        [Continue →]          │    │
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │    DATE + TIME PICKER        │    │  ← Horizontal scroll dates
│  │  "When works for you?"       │    │     Time slots as chips
│  │  [Mon] [Tue] [Wed] [Thu]...  │    │     Only dates with availability
│  │   17  18  19  20  21  22  23 │    │
│  │  ┌──────┐┌──────┐┌──────┐   │    │
│  │  │ 9:00 ││11:30 ││17:00 │   │    │
│  │  └──────┘└──────┘└──────┘   │    │
│  │        [Continue →]          │    │
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │    CONFIRMATION + FORM       │    │  ← Review + identify
│  │  "Almost there"              │    │
│  │  ┌──────────────────────┐    │    │
│  │  │ Your class: Pilates   │    │    │  Summary card
│  │  │ Wed 18 Jun · 9:00 AM  │    │    │
│  │  └──────────────────────┘    │    │
│  │  Name:    [___________]      │    │  Pre-filled if URL param
│  │  Member#: [___________]      │    │  Pre-filled if URL param
│  │  ┌──────────────────────┐    │    │
│  │  │   SAVE MY SPOT       │    │    │  Primary CTA, full-width
│  │  └──────────────────────┘    │    │  BG: #c43a31
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │    CONFIRMATION (post-submit)│    │  ← Replaces form on success
│  │  ✓ You're booked!            │    │
│  │  Class details + calendar    │    │
│  │  Tips for a great class      │    │
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │  Footer: FlexHaus contact    │    │  ← Minimal, secondary
│  │  [email] · [phone]           │    │
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

### Section Visibility Logic

| Section | Initial State | Becomes Visible When |
|---|---|---|
| Brand bar | Always visible (sticky top) | On load |
| Hero | Always visible | On load |
| Class selector | Visible on load (below hero) | On load |
| Date/time picker | **Hidden** until class selected + Continue tapped | Class selected + Continue |
| Confirmation + form | **Hidden** until date/time selected + Continue tapped | Date/time selected + Continue |
| Success state | **Hidden** until form submitted successfully | Successful booking |
| Footer | Always visible | On load |

---

## 4. Component Specs

### 4.1 Brand Bar

```
┌──────────────────────────────────────┐
│        [FLEXHAUS — wordmark]          │  ← Centered, 48px height
└──────────────────────────────────────┘     BG: #1a1a1a, Text: #f0ece4
```

| Property | Value |
|---|---|
| Height | 48px |
| Background | `#1a1a1a` |
| Logo | FlexHaus wordmark in `#f0ece4` (text-based, no image required) |
| Font | Space Grotesk, 700 weight, 14px, letter-spacing: 0.1em, uppercase |
| Position | Sticky top (follows scroll) |
| Z-index | 100 (above all content) |
| Border-bottom | 1px solid `rgba(240,236,228,0.1)` (subtle) |

### 4.2 Hero Section

**Three variants, driven by `?entry=` param:**

#### Variant A: `at-risk`
```
Headline:    "We saved your spot, [Name]"      ← if name in URL, else "We saved your spot"
Subhead:    "Your next class is the one that makes it a habit.
             Pick a time and we'll handle the rest."
CTA:         [Pick My Class →]
```

#### Variant B: `no-show`
```
Headline:    "Fresh start. New class."
Subhead:    "Life happens. Here's a different class you might love.
             No judgment, just a spot waiting for you."
CTA:         [See Available Classes →]
```

#### Variant C: `inactive` (also DEFAULT)
```
Headline:    "Your next class is waiting"
Subhead:    "Members who take a second class within a week are 3x more
             likely to stay. This one's on us."
CTA:         [Find Your Class →]
```

| Property | Value |
|---|---|
| Background | `#f0ece4` |
| Padding | 48px 24px 40px 24px (mobile), 80px 48px 64px 48px (desktop) |
| Headline font | Space Grotesk, 700, 32px/1.15 (mobile), 48px/1.1 (desktop), `#1a1a1a` |
| Subhead font | Space Grotesk, 400, 16px/1.5 (mobile), 18px/1.5 (desktop), `#1a1a1a` at 70% opacity |
| CTA button | `#c43a31` background, `#f0ece4` text, 16px font, 700 weight, 56px height, 16px border-radius, full-width mobile / max 320px desktop |
| CTA hover | Darken to `#a8322a` |
| CTA transition | 200ms ease background-color |
| Max width (content) | 640px centered |
| Decorative element | Thin red line (`#c43a31`, 2px, 40px wide) above headline (optional — add if it feels right) |

### 4.3 Class Type Selector

```
┌──────────────────────────────────────────────┐
│  What kind of class?                          │  ← Section label, small caps
│                                               │
│  ┌──────────┐  ┌──────────┐                   │
│  │          │  │          │                   │
│  │ PILATES  │  │   HIIT   │                   │  ← 2x2 grid mobile
│  │  ★ Rec.  │  │  ★ Rec.  │                   │     4-col row desktop
│  │          │  │          │                   │
│  │ 45 min   │  │ 40 min   │                   │
│  └──────────┘  └──────────┘                   │
│  ┌──────────┐  ┌──────────┐                   │
│  │   YOGA   │  │  SPIN    │                   │
│  │          │  │          │                   │
│  │ 60 min   │  │ 45 min   │                   │
│  └──────────┘  └──────────┘                   │
│                                               │
│              [Continue →]                     │  ← Hidden until a card is selected
└──────────────────────────────────────────────┘
```

| Property | Value |
|---|---|
| Section background | `#ffffff` (white card on warm background) |
| Section padding | 32px 24px |
| Margin from hero | 24px |
| Section label | Space Grotesk, 400, 12px, uppercase, letter-spacing: 0.08em, `#1a1a1a` at 50% |
| Card size | Mobile: full-width of 2-col grid (~calc(50%-8px)). Desktop: ~200px wide |
| Card border | 1.5px solid `#e0dcd4` (default), 2px solid `#c43a31` (selected) |
| Card border-radius | 12px |
| Card padding | 20px 16px |
| Card background (default) | `#faf9f7` |
| Card background (selected) | `#fef5f4` (very light red tint — `#c43a31` at ~5% opacity) |
| Card transition | 200ms ease all |
| Class name | Space Grotesk, 700, 18px, `#1a1a1a` |
| Duration | Space Grotesk, 400, 13px, `#1a1a1a` at 60% |
| "Recommended" badge | Small tag in top-right corner of card: bg `#c43a31` at 15%, text `#c43a31`, 10px, 700 weight, 4px border-radius, padding 2px 8px. Only on Pilates and HIIT cards. |
| "Recommended" rationale | Research shows Pilates and HIIT have lower churn rates. These classes build community and visible progress fast. |
| Continue button | Same style as hero CTA but 48px height. Hidden (`display: none` or `visibility: hidden`) until a card is selected. Appears with a subtle fade-in (200ms). |
| Card hover (desktop) | Border shifts to `#c43a31` at 50%, subtle scale(1.02) |
| Selected state indicator | Checkmark icon appears top-left of card, `#c43a31` |

**Class Types to Display (hardcoded for MVP):**

| Class | Duration | Recommended? | Why |
|---|---|---|---|
| Pilates | 45 min | Yes | High retention, builds visible progress |
| HIIT | 40 min | Yes | Community-driven, fast results |
| Yoga | 60 min | No | Moderate retention |
| Spin | 45 min | No | Moderate retention |

If the Maker needs to make these dynamic via a simple JSON config, that's fine — but hardcoded is acceptable for the €2K/1-month budget.

### 4.4 Date + Time Picker

```
┌──────────────────────────────────────────────┐
│  When works for you?                          │
│                                               │
│  ← scroll →                                   │
│  ┌─────┐┌─────┐┌─────┐┌─────┐┌─────┐┌─────┐ │
│  │ MON ││ TUE ││ WED ││ THU ││ FRI ││ SAT │ │  ← 7-day rolling window
│  │17/6 ││18/6 ││19/6 ││20/6 ││21/6 ││22/6 │ │     from today
│  │ 3 ● ││ 2 ● ││ 4 ● ││ 1 ● ││ 5 ● ││ 2 ● │ │     ● = available slots count
│  └─────┘└─────┘└─────┘└─────┘└─────┘└─────┘ │
│                                               │
│  Available times for Wed 19/6:                │
│  ┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐    │
│  │ 7:00  │ │ 9:00  │ │ 12:00 │ │ 17:30 │    │  ← Time chips
│  │  AM   │ │  AM   │ │  PM   │ │  PM   │    │
│  └───────┘ └───────┘ └───────┘ └───────┘    │
│                                               │
│              [Continue →]                     │
└──────────────────────────────────────────────┘
```

| Property | Value |
|---|---|
| Section background | `#ffffff` |
| Section padding | 32px 24px |
| Date row | Horizontal scroll container with snap points. Show 7 days from today. Each date is a tappable pill: 64px wide, 80px tall. |
| Date pill (default) | Border: 1.5px `#e0dcd4`, bg: `#faf9f7`, border-radius: 12px |
| Date pill (selected) | Border: 2px `#c43a31`, bg: `#fef5f4` |
| Date pill (today) | Subtle indicator: small dot or "Today" label in `#c43a31` |
| Date pill (unavailable) | Opacity: 40%, not tappable. Show if no slots for that class type. |
| Day label | Space Grotesk, 400, 11px, uppercase, `#1a1a1a` at 50% |
| Date number | Space Grotesk, 700, 22px, `#1a1a1a` |
| Slot count | Small text: "3 spots", Space Grotesk, 400, 10px, `#1a1a1a` at 50% |
| Time chips | Horizontal row, wrapping. Each chip: 90px wide, 44px tall, border-radius: 8px, border 1.5px `#e0dcd4`, bg `#faf9f7`. Text: Space Grotesk, 500, 14px. |
| Time chip (selected) | Border 2px `#c43a31`, bg `#fef5f4` |
| Time chip (unavailable/full) | Opacity 30%, strikethrough or "Full" label, not tappable |
| Continue button | Same as class selector Continue: 48px, hidden until date + time both selected |
| Loading state | Skeleton placeholders for dates and times while data loads (see Section 5) |

**Time Slot Logic:**
- Only show slots that exist for the selected class type on the selected date
- Show slots for the next 7 days only
- If a date has no available slots for the selected class type, show it as unavailable (dimmed)
- MVP: Use a static JSON schedule embedded in the page. Maker can load from a `<script type="application/json">` block so it's easy to swap later.

### 4.5 Confirmation + Identifier Form

```
┌──────────────────────────────────────────────┐
│  Almost there                                 │
│                                               │
│  ┌────────────────────────────────────────┐   │
│  │  🏷️  Pilates                          │   │  ← Summary card
│  │     Wednesday 19 June · 9:00 AM       │   │     Icon + class name
│  │     45 minutes                        │   │     Date, time, duration
│  │     FlexHaus Studio — Main Room       │   │     Location
│  └────────────────────────────────────────┘   │
│                                               │
│  First Name                                   │
│  ┌────────────────────────────────────────┐   │
│  │ Sarah___________________________       │   │  ← Pre-filled if URL param
│  └────────────────────────────────────────┘   │
│                                               │
│  Member ID (from your welcome email)          │
│  ┌────────────────────────────────────────┐   │
│  │ FH-12345_________________________      │   │  ← Pre-filled if URL param
│  └────────────────────────────────────────┘   │
│                                               │
│  ☐  Send me a reminder 30min before class    │  ← Checked by default
│                                               │
│  ┌────────────────────────────────────────┐   │
│  │          SAVE MY SPOT                  │   │  ← Full-width, #c43a31
│  └────────────────────────────────────────┘   │
│                                               │
│  By booking you agree to our 4hr             │
│  cancellation policy.                         │
└──────────────────────────────────────────────┘
```

| Property | Value |
|---|---|
| Section background | `#ffffff` |
| Section padding | 32px 24px |
| Summary card | Background: `#f0ece4`, border-radius: 10px, padding: 16px, border-left: 4px solid `#c43a31` |
| Summary icon | Small icon (emoji or SVG) left of class name |
| Summary class name | Space Grotesk, 700, 16px, `#1a1a1a` |
| Summary details | Space Grotesk, 400, 13px, `#1a1a1a` at 70%, one detail per line |
| Input labels | Space Grotesk, 400, 12px, uppercase, letter-spacing: 0.05em, `#1a1a1a` at 60%, margin-bottom: 6px |
| Input fields | Height: 48px, border: 1.5px `#e0dcd4`, border-radius: 8px, padding: 0 14px, font: Space Grotesk 400 16px, bg: `#faf9f7`. Focus: border `#c43a31`, outline none, bg: `#ffffff` |
| Checkbox row | Space Grotesk, 400, 14px, `#1a1a1a`. Custom checkbox styled in `#c43a31`. |
| "Save My Spot" CTA | Height: 56px, bg: `#c43a31`, color: `#f0ece4`, font: Space Grotesk 700 16px, border-radius: 12px, full-width, border: none, cursor: pointer, transition: 200ms ease bg |
| CTA hover | `#a8322a` |
| Disclaimer | Space Grotesk, 400, 11px, `#1a1a1a` at 40%, centered, 12px margin-top |
| Spacing between form groups | 20px |

**Identifier Logic:**
- If `?name=` is present, pre-fill the name field. Member can edit.
- If `?memberId=` is present, pre-fill. Member can edit.
- If neither is present, both fields are empty. Validation on submit: both required.
- The member ID field accepts alphanumeric + hyphens. Format hint: "e.g., FH-12345"

### 4.6 Success / Confirmation State

```
┌──────────────────────────────────────────────┐
│                                              │
│              ✓  (animated checkmark)          │  ← CSS-drawn or SVG
│                                              │
│           You're booked, [Name]!              │  ← Space Grotesk 700 28px
│                                              │
│     ┌──────────────────────────────────┐     │
│     │  Pilates                         │     │
│     │  Wednesday 19 June · 9:00 AM     │     │  ← Confirmation card
│     │  45 min · Main Room              │     │
│     │                                  │     │
│     │  [Add to Calendar]  ←  ical link │     │  ← Text button, secondary
│     └──────────────────────────────────┘     │
│                                              │
│  ┌──────────────────────────────────────┐    │
│  │  💡  Good to know                    │    │
│  │                                      │    │  ← Tips card
│  │  • Arrive 10 min early to settle in  │    │     Warm, helpful
│  │  • Bring a water bottle and towel    │    │     Not scary
│  │  • The studio is at [address]        │    │
│  │  • We'll send a reminder 30 min      │    │
│  │    before class                      │    │
│  └──────────────────────────────────────┘    │
│                                              │
│         [Back to FlexHaus Home]              │  ← Tertiary link
│                                              │
└──────────────────────────────────────────────┘
```

| Property | Value |
|---|---|
| Section background | `#f0ece4` |
| Success section | Replaces the entire booking flow (hero stays, everything below hero swaps) |
| Checkmark animation | A circular stroke-dash animation (SVG) — draws a circle then a checkmark. Duration: ~600ms, slight bounce ease. Color: `#c43a31`. |
| Confirmation card | Same style as summary card but standalone. Border-left: 4px `#c43a31`, bg: `#ffffff` |
| "Add to Calendar" | Generates/downloads an `.ics` file. Styled as a text link: Space Grotesk, 500, 14px, `#c43a31`, underlined. |
| Tips card | Background: `#ffffff`, border-radius: 10px, padding: 20px. Header: Space Grotesk, 700, 15px. Tips: Space Grotesk, 400, 14px, line-height 1.6. Bullet markers: small `#c43a31` dots. |
| Home link | Centered, Space Grotesk, 400, 14px, `#1a1a1a` at 60%, underlined. Links to FlexHaus main site. |

---

## 5. Interaction States

### 5.1 Loading States

| Context | Behavior |
|---|---|
| Page load (before hero renders) | Full-page skeleton: grey placeholder blocks matching layout shape. 800ms max. |
| Class type cards loading | 4 skeleton cards (pulsing grey rectangles, 200px × 140px). Shown for <500ms. |
| Date/time slots loading | Skeleton pills in date row (7 grey ovals), skeleton chips below (4 grey rectangles). |
| Form submission (saving) | "Save My Spot" button → spinner + "Saving..." text. Button disabled during submission. Spinner is a small CSS circle in `#f0ece4`. Duration: typically 1-2 seconds. |

**Skeleton animation:** Subtle shimmer pulse — background animates from `#e8e5df` to `#f0ece4` and back over 1.5s, infinite. Use `background: linear-gradient(90deg, #e8e5df 25%, #f0ece4 50%, #e8e5df 75%)` with `background-size: 200% 100%` and animation.

### 5.2 Empty States

| Context | Behavior |
|---|---|
| No class types available | Show message: "No classes available right now. Check back soon or call the studio." with studio phone number. |
| No time slots for selected date + class | Show message: "All slots are full for this date. Try another day or a different class type." The date pill for that day should already be dimmed if possible. |
| No dates available at all (edge case) | Show message: "We're updating the schedule. Check back tomorrow or call us at [phone]." |

### 5.3 Error States

| Context | Behavior |
|---|---|
| Name field empty on submit | Red border on field (`#c43a31`), small error text below: "We need your name to save your spot." Shake animation (subtle, 3px horizontal, 300ms). |
| Member ID empty on submit | Red border, error text: "Enter your member ID — it's in your welcome email." |
| Member ID format invalid | Red border, error text: "That doesn't look right. It should be like FH-12345." |
| Booking fails (server error) | Inline error banner at top of form section: `#c43a31` bg at 10%, `#c43a31` text, icon: ⚠. Text: "Something went wrong. Please try again or call the studio at [phone]." |
| Network error / timeout | Same banner style: "We couldn't reach the booking system. Check your connection and try again." |
| Booking already exists (duplicate) | Success-ish message: "You're already booked for this class! We'll send you a reminder." (treat as success, don't error out). |
| Link expired / invalid member | If the personalized link has an invalid/expired token: "This link has expired. No worries — book directly at flexhaus.com/book or call us." |

### 5.4 Success State

See Section 4.6 above. Key behaviors:
- Confirmation section fades in over 400ms
- Checkmark animation plays once on entry
- Scroll to top of confirmation section automatically
- "Add to Calendar" triggers `.ics` download (not a navigation)

### 5.5 Edge Cases

| Case | Handling |
|---|---|
| Member uses browser back button after booking | Show the confirmation state, not the form. Detect via checksum or sessionStorage flag. |
| Member refreshes mid-flow | Show hero + class selector fresh. Do NOT preserve mid-flow state (simpler, less bug-prone). The link is still valid; they start over. |
| Very long name (30+ chars) | Input field truncates visually with ellipsis but preserves full value. No char limit enforced. |
| Member ID with unusual format | Accept any alphanumeric + hyphens + underscores. Don't be too strict. The backend validates. |
| Member arrives without any URL params | Generic hero (variant C), empty name + ID fields. Fully functional. |
| Screen reader / keyboard nav | All interactive elements must be focusable, labeled, and operable via keyboard. Use semantic HTML: `<button>`, `<input>`, `aria-label` on icon-only elements. |

---

## 6. Mobile Layout Adaptations

### Breakpoints

| Breakpoint | Width | Behavior |
|---|---|---|
| Mobile | 320px – 767px | Single column, stacked layout |
| Tablet | 768px – 1023px | 2-column grid for cards, slightly larger type |
| Desktop | 1024px+ | Wider max-width container, 4-column class cards, side-by-side where appropriate |

### Mobile-Specific Adaptations

| Element | Mobile Behavior |
|---|---|
| Page container | Full-width, no side margins except 16px padding |
| Hero headline | 28px font (down from 32px on tablet) to avoid wrapping on small screens |
| CTA buttons | Full-width always (not max-width constrained) |
| Class type cards | 2×2 grid. Each card: `calc(50% - 8px)` wide. Gap: 16px. |
| Date picker | Horizontal scroll with snap. Dates extend beyond viewport width. Scroll indicator: subtle fade on right edge to indicate more content. |
| Time chips | Wrap naturally in a flex row. Max 3 per row then wrap. |
| Form inputs | Full-width. Labels above inputs (not side-by-side). |
| Confirmation | Single column. Tips card stacks below confirmation card. |
| Touch targets | All tappable elements minimum 44px × 44px (Apple HIG). |
| Font scaling | Headlines use `clamp()` for fluid sizing between 320px–768px |
| Sticky brand bar | Remains sticky on mobile. Compress to 44px height on very small screens (<360px). |

### Desktop-Specific Adaptations

| Element | Desktop Behavior |
|---|---|
| Page container | Max-width: 720px, centered, with generous padding |
| Hero headline | 48px font |
| Class type cards | Single row, 4 across, equal width via flex/grid |
| Date picker | No horizontal scroll needed — all 7 dates visible in one row |
| Form | Input fields can be side-by-side (name + member ID in a 2-col row) |
| Hover states | All interactive elements get hover effects (desktop only — no hover on touch) |

---

## 7. Color & Typography Reference

### Color Palette

| Token | Hex | Usage |
|---|---|---|
| `--color-black` | `#1a1a1a` | Primary text, brand bar background |
| `--color-red` | `#c43a31` | Primary accent, CTAs, selected states, error indicators |
| `--color-red-hover` | `#a8322a` | CTA hover state |
| `--color-red-tint` | `#fef5f4` | Selected card/chip background (red at ~5%) |
| `--color-red-badge` | `rgba(196,58,49,0.15)` | "Recommended" badge background |
| `--color-warm-bg` | `#f0ece4` | Page background, hero section background |
| `--color-white` | `#ffffff` | Card/section backgrounds |
| `--color-card-bg` | `#faf9f7` | Default card/chip background |
| `--color-border` | `#e0dcd4` | Default borders, dividers |
| `--color-text-muted` | `rgba(26,26,26,0.5)` | Secondary text, labels |

### Typography

| Token | Value |
|---|---|
| Font family | `'Space Grotesk', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif` |
| Weights used | 400 (regular), 500 (medium), 700 (bold) |
| Headline (mobile) | 700, 28-32px, line-height: 1.15 |
| Headline (desktop) | 700, 48px, line-height: 1.1 |
| Subheadline | 400, 16-18px, line-height: 1.5 |
| Section labels | 400, 12px, uppercase, letter-spacing: 0.08em |
| Body | 400, 14-16px, line-height: 1.5 |
| Buttons/CTAs | 700, 16px, letter-spacing: 0.02em |
| Small/disclaimer | 400, 11px |

### Spacing Scale

| Token | Value | Usage |
|---|---|---|
| `--space-xs` | 8px | Tight gaps, icon padding |
| `--space-sm` | 12px | Inline spacing |
| `--space-md` | 16px | Card padding, form gaps |
| `--space-lg` | 24px | Section padding (mobile) |
| `--space-xl` | 32px | Section spacing, large gaps |
| `--space-2xl` | 48px | Hero top padding |
| `--space-3xl` | 80px | Hero top padding (desktop) |

---

## 8. Key Design Decisions

### Decision 1: Progressive Disclosure Instead of Multi-Page Wizard

**Chosen:** Single page with scroll-to-reveal sections.

**Why:** Research shows these members are already disengaged. Every additional page load is a dropout opportunity. A single page with progressive reveal keeps momentum: each completed step scrolls smoothly to the next without a full navigation. The URL never changes — back-button behavior is predictable.

**Alternative considered:** Multi-step wizard (3 pages). Rejected because page transitions add load time and break the "effortless" feeling we need for retention.

### Decision 2: "Recommended" Badge on Pilates and HIIT

**Chosen:** Visual recommendation signals on lower-churn class types.

**Why:** The research brief explicitly calls out that certain class types have higher retention. Rather than hiding this insight or making it a separate educational section, we embed it directly into the choice architecture as a gentle nudge. The badge is subtle (not salesy), and members can still choose Yoga or Spin if they prefer.

**Alternative considered:** Sorting all classes by retention rate. Rejected because it would make the selector feel manipulative. The badge preserves choice while guiding.

### Decision 3: Date Picker Shows Only Next 7 Days

**Chosen:** 7-day rolling window from today.

**Why:** Research shows the 7-day gap between first and second booking is the critical churn signal. Showing more dates creates paradox of choice and lets members kick the can down the road. Constraining to 7 days creates gentle urgency aligned with the behavioral insight. It also matches the 7-day nudge timing.

**Alternative considered:** Full month calendar view. Rejected: too much choice, dilutes urgency, and the research specifically calls out 7 days as the magic number.

### Decision 4: Pre-filled Name/ID from URL Params

**Chosen:** Parse `?name=` and `?memberId=` from the personalized link and pre-fill.

**Why:** These members arrive via SMS/email. The link is already personalized. Pre-filling removes friction — they verify, they don't type from scratch. This is especially important on mobile where typing is slow and error-prone.

**Alternative considered:** Auto-submit without showing the form. Rejected: members should see and confirm their booking details. Full invisibility erodes trust.

### Decision 5: The "Save My Spot" Language

**Chosen:** CTA says "Save My Spot" not "Book Now" or "Reserve."

**Why:** "Save" implies something limited and valuable. "My" is possessive — this is their spot, we're holding it. This language aligns with the at-risk entry point messaging ("We saved your spot") and creates continuity across the experience. It's warmer than transactional booking language.

### Decision 6: Mobile-First, Not Mobile-Responsive

**Chosen:** Design for 375px width first, then scale up.

**Why:** The majority of these links will be opened on mobile (SMS is inherently mobile; email is ~60%+ mobile opens). The layout, tap targets, and collapsed sections are all optimized for phone screens. Desktop gets enhancements but isn't the primary target.

### Decision 7: Tips Card in Confirmation, Not Before

**Chosen:** Show "Good to know" tips after booking, not before.

**Why:** Tips before booking can feel like barriers ("oh, I need to bring a towel? maybe I'm not prepared"). Tips after booking feel like helpful onboarding. The emotional order matters: first reduce anxiety about booking, then help them prepare.

---

## 9. Spec for Maker (Unambiguous Build Instructions)

### What to Build

A single, self-contained HTML file (`booking.html`) with embedded CSS and JavaScript. No build step, no framework, no external dependencies except:
- Google Fonts: Space Grotesk (weights 400, 500, 700) loaded via `<link>` in `<head>`
- A single `.ics` calendar file generated client-side for the "Add to Calendar" feature

### File Structure

```
booking.html          ← Everything in one file
```

All CSS in a `<style>` block. All JS in a `<script>` block at the end of `<body>`. Use vanilla JS — no jQuery, no frameworks.

### URL Parameters to Parse

| Param | Required? | Format | Example |
|---|---|---|---|
| `entry` | No | One of: `at-risk`, `no-show`, `inactive` | `?entry=at-risk` |
| `name` | No | URL-encoded string | `?name=Sarah` |
| `memberId` | No | Alphanumeric + hyphens | `?memberId=FH-12345` |

### Class Schedule Data

Embed a static JSON object in a `<script type="application/json" id="schedule-data">` tag. Structure:

```json
{
  "classes": [
    {
      "id": "pilates",
      "name": "Pilates",
      "duration": "45 min",
      "recommended": true,
      "location": "Main Room"
    },
    { "id": "hiit", "name": "HIIT", "duration": "40 min", "recommended": true, "location": "Main Room" },
    { "id": "yoga", "name": "Yoga", "duration": "60 min", "recommended": false, "location": "Studio B" },
    { "id": "spin", "name": "Spin", "duration": "45 min", "recommended": false, "location": "Spin Studio" }
  ],
  "slots": [
    { "classId": "pilates", "date": "2026-06-17", "time": "07:00", "available": 3 },
    { "classId": "pilates", "date": "2026-06-17", "time": "09:00", "available": 5 },
    { "classId": "pilates", "date": "2026-06-17", "time": "17:30", "available": 2 },
    { "classId": "hiit", "date": "2026-06-17", "time": "08:00", "available": 4 },
    { "classId": "hiit", "date": "2026-06-17", "time": "12:00", "available": 6 },
    { "classId": "yoga", "date": "2026-06-17", "time": "10:00", "available": 8 },
    { "classId": "yoga", "date": "2026-06-17", "time": "18:00", "available": 3 },
    { "classId": "spin", "date": "2026-06-17", "time": "06:30", "available": 10 },
    { "classId": "spin", "date": "2026-06-17", "time": "16:00", "available": 7 }
  ]
}
```

Generate 7 days of slots for all 4 class types in the embedded JSON. Vary availability counts (1-10). Include at least 2 dates per class type with zero slots (to test the empty/unavailable state).

### Behavior Rules (JS Logic)

1. **On page load:**
   - Parse URL params
   - Determine hero variant (at-risk / no-show / inactive / default)
   - Render hero with correct headline + subheadline
   - Pre-fill name and member ID fields if params present
   - Render class type cards from schedule data
   - Hide date picker, confirmation form, and success state

2. **On class type selected:**
   - Add `selected` visual state to tapped card
   - Remove `selected` from any previously selected card
   - Show Continue button (fade in)
   - On Continue tap: scroll date picker into view, render available dates for that class type

3. **On date selected:**
   - Add `selected` visual state to date pill
   - Filter and render time chips for that date + class type
   - Clear any previously selected time
   - Hide Continue button until time is also selected

4. **On time selected:**
   - Add `selected` visual state to time chip
   - Show Continue button
   - On Continue tap: scroll confirmation form into view, populate summary card with selected class + date + time

5. **On form submit:**
   - Validate name and member ID are non-empty
   - Show error states if validation fails
   - If valid: show loading state on button (spinner + "Saving..."), simulate a 1-2s delay (for MVP, since there's no backend), then show success state
   - On success: replace form section with confirmation section, scroll to top of confirmation

6. **On "Add to Calendar":**
   - Generate an `.ics` string client-side
   - Create a Blob and trigger download
   - File name: `flexhaus-{class-name}-{date}.ics`

### Semantic HTML Requirements

- Use `<header>`, `<main>`, `<section>`, `<form>`, `<button>` (not div-as-button)
- All form inputs must have `<label>` elements (visible or `sr-only`)
- Use `type="text"` for name, `type="text"` for member ID
- Checkbox: use `<input type="checkbox">` with styled custom appearance
- No empty links (`<a href="#">`). Use `<button>` for actions.
- Add `aria-live="polite"` region for dynamic content updates (error messages, slot changes)
- Focus management: when new sections appear, move focus to the section heading

### CSS Architecture

- Mobile-first: base styles target 320px+ screens
- Use `@media (min-width: 768px)` for tablet, `@media (min-width: 1024px)` for desktop
- Use CSS custom properties (variables) for all colors and spacing (see Section 7)
- No `!important` unless absolutely necessary
- Smooth scroll behavior: `scroll-behavior: smooth` on `html`
- Section transitions: use `opacity` + `transform: translateY(8px)` for section reveal animations (300ms ease-out)
- Selected states: use a `.selected` class, not inline styles
- Skeleton loading: use a `.skeleton` class with the shimmer animation described in Section 5.1

### What NOT to Build

- No backend integration (the form submit is a simulated success after delay)
- No actual email/SMS sending
- No analytics/tracking (the brief says retention, not measurement — measurement would be a separate Yellow Analyst task)
- No authentication or session management
- No Stripe/payment integration (these are existing members, not new signups)

---

## 10. Constraints

### Technical Constraints

| Constraint | Detail |
|---|---|
| File format | Single `.html` file. No CSS/JS files, no build tools. |
| Dependencies | Google Fonts only. No npm, no CDN libraries, no frameworks. |
| Browser support | Modern browsers only: Chrome 90+, Safari 15+, Firefox 90+, Edge 90+. No IE11. |
| JavaScript | Vanilla ES6+. No TypeScript, no JSX, no transpilation. |
| Backend | None. The page simulates a booking submission with a 1-2s delay then shows success. Maker must add a `TODO` comment where the real API call would go. |
| Performance | Page must load and be interactive in under 2 seconds on 4G mobile. Total file size under 100KB (including embedded JSON). |

### Scope Boundaries

| In Scope | Out of Scope |
|---|---|
| Booking flow UI + interaction logic | Actual booking backend / database |
| Personalized hero messaging (3 variants) | A/B testing framework for variants |
| Static class schedule (embedded JSON) | Dynamic schedule from a CMS |
| Client-side form validation | Server-side validation |
| `.ics` calendar file generation | Calendar integration / API |
| Mobile, tablet, desktop layouts | Native mobile app |
| Accessibility basics (labels, focus, aria-live) | Full WCAG 2.1 AA audit |

### Budget Constraint Context

The €2,000 / 1-month budget means:
- Keep the scope tight — this is an MVP intervention, not a platform
- Hardcoded schedule data is acceptable (it can be updated manually for one month)
- No backend development is budgeted — the simulated submit is intentional
- The Maker should complete this in a single focused session, not a multi-week build

### Dependencies

- **Research brief** (already delivered by Yellow Researcher) — used for behavioral insights and entry point definitions
- **FlexHaus brand assets** — logo as text wordmark (no image file needed), colors and font as specified
- **Class schedule data** — to be created by Maker as embedded JSON (sample data is fine for MVP)
- **SMS/email campaign** — out of scope for this design. The page is the destination; the outbound messaging is a Green Marketer task.

---

## 11. Design-to-Research Traceability Matrix

Every major design decision maps back to a research finding:

| Design Element | Research Insight | How It Addresses It |
|---|---|---|
| 7-day date window | 7-day gap between 1st and 2nd booking is a churn signal | Creates urgency aligned with the behavioral window; prevents procrastination |
| "Recommended" badge on Pilates/HIIT | Certain class types have lower churn | Nudges members toward higher-retention options without removing choice |
| Personalized hero (3 variants) | Different churn triggers need different messaging | No-show needs reassurance (not guilt); inactive needs a nudge; at-risk needs saved-spot framing |
| Pre-filled name + member ID | Friction is the enemy of re-engagement | Removes typing on mobile; members only verify, not enter from scratch |
| "Save My Spot" language | Members need to feel valued, not reprimanded | Possessive + scarcity framing; warmer than "Book Now" |
| Single page, no reload | Each page transition is a dropout opportunity | Keeps momentum; reduces cognitive load; faster to complete |
| Mobile-first | SMS/email links opened primarily on phones | Optimized for the actual device context of the intervention |
| Tips after booking, not before | Pre-booking info can feel like barriers | Reduces anxiety during booking; provides helpful onboarding after commitment |
| Identifier by name + member ID (no password) | These are existing members, not new signups | No auth barrier; the personal link + member ID combo is sufficient trust for rebooking |
| No guilt messaging anywhere | At-risk members are already disengaged | Guilt accelerates churn. Warm/fresh-start framing rebuilds the relationship |

---

## 12. Open Questions for Manager

1. **Member ID format:** Is the member ID format standardized as `FH-XXXXX` or does it vary? The spec assumes `FH-` prefix but the validation is loose. If there's a strict format, I need to know it.

2. **Class schedule authenticity:** Should the Maker generate 7 days of plausible dummy data, or is there a real FlexHaus schedule to embed? The spec uses dummy data with a note that it's swappable.

3. **Green Marketer handoff:** The outbound SMS/email copy that drives members to this page is outside my scope. Should I flag to the Manager that the Green Marketer needs to write the personalized link copy for each entry point?

4. **Success metric:** What defines success for this page? Booking completion rate? Actual attendance rate? Second-class retention at 30 days? I've designed for completion rate, but the Manager may want the Yellow Analyst to define evaluation criteria.

---

STATUS: completed
ARTIFACTS: [".octopus/designs/control-retention-page-spec.md"]
SUMMARY: Created a comprehensive design spec for a FlexHaus member-retention booking page. The single-page HTML experience targets lapsed and at-risk members at three churn moments (one-and-done, first-class no-show, 7+ day booking gap). Design uses progressive disclosure (hero → class selector → date/time picker → confirmation form → success state) on a single scrollable page. Key decisions: 7-day date window matching the research-identified churn signal, "Recommended" badges nudging toward lower-churn class types (Pilates/HIIT), pre-filled identifiers from URL params to reduce mobile friction, and "Save My Spot" language for warm/possessive framing. Mobile-first with Space Grotesk typography and FlexHaus brand colors (#1a1a1a, #c43a31, #f0ece4). Includes full interaction states (loading, empty, error, success, edge cases), CSS variable system, and a traceability matrix linking every design decision to specific research findings.
BLOCKERS: none
HANDOFF READY: yes
---