# cold-verify.md — FlexHaus Comeback Campaign: Cold Verification

**Auditor:** Yellow (Cold Verifier — blind, independent)
**Date:** 2026-06-16
**Assets Audited:**
1. `.octopus/briefs/diagnosis.md` — Tara's churn diagnosis
2. `.octopus/designs/design-spec.md` — Mateo's design spec
3. `real/index.html` — Kojo's built page
4. `.octopus/handoffs/marketing.md` — Róisín's win-back copy
5. `.octopus/briefs/member_panel.csv` — Rebuilt member panel

**Criteria:** Frozen per `context_rich.md` §7.

---

## SCORECARD

### 1. Diagnosis is data-bound — **PASS**

- ✅ **Member panel rebuilt:** `member_panel.csv` exists, contains exactly 1,556 data rows (matching the diagnosis claim of 1,556 distinct members), with 18 columns matching the fields in `flexhaus_bookings.csv` schema (§2.6).
- ✅ **Precise at-risk rule stated** (diagnosis §3.1): *"ACTIVE AND (total_bookings ≤ 2 OR days_since_last_booking ≥ 30)"* — operationally defined, verifiable.
- ✅ **Headcount:** 195 at-risk members (30.6% of 637 active).
- ✅ **€/year figure with visible maths** (diagnosis §3.2): Sum of monthly fees = €8,797/mo → ×12 = €105,564/year. Maths shown in full.
- ✅ **~2× cliff shown** (diagnosis §2.2): 75.3% cancel for 1-booking vs 38.1% for 5+ bookings = **1.98× lift**. Query stated in PowerShell pseudo-code. Data table with all four buckets.
- ✅ **Reconciles with §2 summaries:** Active members 637 ≈ ~637 ✓; one-and-done 42.1% ≈ ~42% ✓; no-show rate 7.0% ≈ ~7% ✓; avg fee €44.97 ≈ €45.26 ✓.
- ⚠ **Flagged by Tara herself:** The 2–4 booking bucket distribution (8.9% computed vs ~27% benchmark) and MRR (€28,645 vs ~€30,800) show discrepancies. Tara documents both transparently in §6.1–6.2. These do not affect core findings since the at-risk rule and churn lift are computed from data, not the summary tables.

**Verdict: PASS.** The diagnosis is grounded in the CSV, names the segment, shows the maths, flags its own discrepancies.

---

### 2. One metric, not five — **PASS**

- ✅ **Single primary metric named** in three places:
  - Diagnosis §7: *"Comeback-Class Bookings — at-risk members who book AND attend a class within 30 days of receiving the first win-back message."*
  - Design spec §0: *"The ONE metric it serves: Comeback-class bookings."*
  - Marketing.md §0: *"The ONE metric: Comeback-class bookings within 30 days."*
- ✅ **Everything serves it:** The page has ONE CTA (book a class). The messages have ONE CTA (link to the booking page). No secondary calls-to-action (no newsletter, no social follow, no refer-a-friend) distract from the booking goal.
- ✅ **Measurable within the one-month window:** 30-day booking window is directly observable.
- ✅ **Revenue-traceable:** Each comeback booking saves ≈€45/mo (€540/year per saved member) — diagnosis §7.

**Verdict: PASS.** One metric, consistently named, everything serves it.

---

### 3. The page actually runs on a phone — **PASS with minor gaps**

| Requirement | Status | Evidence |
|---|---|---|
| three.js CDN import | ✅ | `importmap` loading `three@0.160.0` from jsdelivr (line 817-823) |
| Mobile viewport meta | ✅ | `<meta name="viewport" content="width=device-width, initial-scale=1.0">` (line 5) |
| DPR cap | ✅ | `Math.min(window.devicePixelRatio, 2)` (line 1034) |
| Device tier detection | ✅ | `navigator.hardwareConcurrency` → high/mid/low tiers with scaled particle counts (lines 853-859) |
| IntersectionObserver for DOM crossfades | ❌ | **Zero `IntersectionObserver` usage in 1,822 lines.** DOM crossfades use `style.opacity` manipulation inside `requestAnimationFrame` (`updateDOMVisibility(s)` at line 1534). Design spec §10 FLAG 5 explicitly mandates: *"DOM section crossfades should use CSS opacity transitions (GPU-composited), NOT JS-driven opacity changes. Use IntersectionObserver to toggle CSS classes."* This instruction was dropped. |
| prefers-reduced-motion | ✅ | CSS `@media (prefers-reduced-motion: reduce)` kills all animations (lines 607-614); JS checks `window.matchMedia('(prefers-reduced-motion: reduce)')` (line 852); body gets `.reduced-motion` class (line 921) |
| no-WebGL fallback | ✅ | `detectWebGL()` function (lines 862-869); `.fallback-mode` class applied (line 916); CSS fallback hero with animated pulse dot (lines 140-186); all DOM sections become visible in document flow (lines 247-265) |
| Form validation | ✅ | `validateForm()` (lines 1584-1617): checks name (≥2 chars), contact (≥5 chars), consent (must be checked); inline error messages with `role="alert"` |
| Consent checkbox UNCHECKED by default | ✅ | `<input type="checkbox" id="consent" name="consent" required>` — no `checked` attribute (line 780) |
| Semantic HTML | ✅ | `<main>` with `<section>` elements, real `<label>` tags with `for`/`id` pairs, `<h1>` → `<h2>` hierarchy, `aria-label` on all sections, `aria-live="assertive"` on success |
| Form wired to provider | ✅ | Formspree endpoint (placeholder) + mailto fallback `dervla@flexhaus.ie` (lines 1567-1678); graceful degradation when Formspree unconfigured |
| All required sections present | ✅ | §1 Hero, §2 Momentum, §3 Proof, §4 Card/Form, §5 Success — all present in DOM |

**Gap:** No `IntersectionObserver` anywhere in the page. The design spec (§10 FLAG 5 and §6.6) required it for GPU-composited crossfades and staggered beat entrance. The current JS-driven opacity approach works functionally but violates the designer's explicit instruction against JS opacity manipulation in rAF. The per-beat staggered entrance (150ms delay between proof beats per §4.2) is also unimplemented — the entire proof section fades as one unit.

**Verdict: PASS with gaps.** The page loads, the 3D renders, the form captures, fallbacks work. IntersectionObserver omission is a fidelity gap, not a functional break.

---

### 4. On-brand, on-voice — **PASS**

- ✅ **"Comeback / we saved you a spot" spine:** Present in hero headline ("Your spot's still here."), momentum section ("It's not a setback. It's a pause."), proof beats (Marta's class "has your name on it"), and win-back copy (all cohorts).
- ✅ **Palette used:** Carbon `#0E1116` (background), Chalk `#F5F5F2` (text), Volt `#C6F24E` (accents, CTA bg, proof beat borders), Ember `#FF5A3C` (submit button, "2×" stat emphasis). All four brand tokens present.
- ✅ **Fonts:** Anton (display/headlines) and Inter (body) loaded from Google Fonts, used consistently.
- ✅ **Tone — warm, not shaming:** No instance of "you've been slacking," "where have you been," or any guilt-mongering found in any copy. Marketing.md explicitly avoids shame (traceability table §4, rows 5, 12, 13). Momentum section reframes lapse as "a pause." All cohort variants use belonging/invitation language.
- ✅ **Not generic:** The Marta/Tuesday 9am/Strength specificity is unique to FlexHaus — no other gym could use this exact combination. The "Show up for yourself" wall quote, the exposed-brick visual concept, the Irish instructor names — all FlexHaus-specific.
- ⚠ **Minor:** The hero stat "Members who skip the second class are 2× more likely to quit" appears in the hero for ALL cohorts (lines 884-897 of index.html), not just Cohort A. This is technically on-brief (harness §2 allows *"one headline stat"* on the hero) but the design spec placed it only in Beat 3 of the Proof section. Not an error, but a deviation from Mateo's information architecture.

**Verdict: PASS.** Page and messages share the spine, palette, fonts, and warm-not-shaming voice. The Marta/Tuesday/Strength specificity makes it unmistakably FlexHaus.

---

### 5. GDPR-clean and kind — **PASS**

- ✅ **Clear opt-out on every message:** SMS messages use "Reply STOP to opt out" (standard, unambiguous). Email messages include unsubscribe links with plain-language text. Marketing.md §5 audit table confirms all 9 messages.
- ✅ **No pre-tick on consent:** Consent checkbox has no `checked` attribute (confirmed by grep: no `checked` in the checkbox HTML; only JS property reads).
- ✅ **No shame:** Marketing.md traceability table §4 rows 5, 12, 13 document avoidance of shame language. All copy audited — no instance of guilt/shame framing found.
- ✅ **No false urgency:** No "limited spots," no countdown timers, no "only X left." Cohort C Touch 3 explicitly says "Whenever you're ready." Marketing.md §4 row 13 confirms.
- ✅ **No dark patterns:** Consent is plain English ("Yes, send me a reminder about this class. I can opt out anytime."). No bundled consent for future marketing. Opt-out does not affect membership (explicitly stated in email copy).
- ✅ **Legal basis documented:** Marketing.md §5.1 identifies legitimate interest as the basis — active paying members receiving service communications about classes they're paying for.

**Verdict: PASS.** GDPR-clean per-message audit, no shame, no false urgency, no pre-ticked consent.

---

### 6. No hallucinated facts — **PASS (all numbers traceable)**

| Claim | Where It Appears | Traced To | CSV-Verified? |
|---|---|---|---|
| "Members who skip the second class are 2× more likely to quit" | Page hero + Beat 3 + marketing Cohort A email | Diagnosis §2.2: 75.3% vs 38.1% = 1.98× → honestly rounded to 2× | ✅ 1.98× lift computed from CSV |
| 195 at-risk members | Diagnosis §3.2, marketing §1 | Diagnosis §3.2: active + (≤2 bookings OR ≥30-day gap) | ✅ Computed from CSV via GROUP BY member_id |
| €8,797/mo at stake | Diagnosis §3.2 | Sum of monthly fees for 195 at-risk members | ✅ Sum over CSV data |
| €105,564/year at stake | Diagnosis §3.2, marketing | €8,797 × 12 | ✅ Derived from CSV sum |
| 42.1% one-and-done rate | Diagnosis §1.2 | 655 / 1,556 members | ✅ Computed from CSV |
| Marta's 64.4% return rate | Diagnosis §5.3, design spec §10, marketing §4 | 139 returned / 216 first-timers with Marta | ✅ Computed from CSV |
| "nearly twice as likely to stay" (Cohort A email) | Marketing.md Touch 2 (Cohort A) | Diagnosis §2.2; traced in marketing §4 row 6 | ✅ Referenced to 1.98× lift |
| All cohort headcounts (43, 115, 27) | Marketing.md §1A–1C | Diagnosis §3.3 sub-segments | ✅ Computed from CSV |

- ✅ The "2×" stat has an HTML comment in the page source tracing it: `<!-- Source: flexhaus_bookings.csv, 75.3% vs 38.1% cancel rate, 1.98x lift -->` (line 662, 687).
- ✅ Marketing.md §4 provides a 15-row traceability table linking every copy decision to a Tara finding or §1 verbatim.
- ✅ No invented testimonials, class-capacity figures, or fabricated stats found in any deliverable.
- ✅ The only `[ASSUMPTION]` markers in diagnosis are for edge cases (zero-booking joiners, reference date, frozen members) — none affect the core numbers.

**Verdict: PASS.** Every number traces to the diagnosis, which traces to the CSV. No fabricated claims.

---

## THE CATCH: One Specific Thing a Specialist Got Wrong

> **BROKEN HANDOFF: Róisín's win-back links pass `?id=[member_id]&slot=tue-9am-strength` but Kojo's page reads only `?cohort=a|b|c`. The URL parameter contract between Marketer and Maker is dropped — zero code in the built page reads `id` or `slot` from the URL. The entire personalisation chain (member-specific pre-fill, slot pre-selection, tracking) is severed.**

**Evidence:**
- Marketing.md every CTA link uses: `[comeback-page-url]?id=[member_id]&slot=tue-9am-strength` (e.g., lines 30, 55, 71, 93, 117, 138, 161, 184, 197)
- `real/index.html` line 876: `const cohort = urlParams.get('cohort') || 'b';` — reads only `cohort`
- `real/index.html` grep for `id`/`member_id`/`slot`: zero matches for URL parameter reading
- The design spec §4.4 expected Formspree to carry these fields, but the URL-to-form bridge (member-specific pre-fill from the personalised link) was never built
- The form defaults are hardcoded HTML (`<option value="Strength" selected>` etc.) with no dynamic pre-fill from URL parameters

**Impact:** When a member taps a win-back link, the page cannot pre-fill their name, known class preferences, or personalise the slot beyond the hardcoded defaults. The `member_id` is lost — no way to track which at-risk member converted. This directly undermines the ONE metric (comeback-class bookings) because you can't tie a booking back to a specific at-risk member without the `id` parameter.

---

## SUMMARY VERDICT

### BLOCKED — one fix required before shipping

**What must be fixed:**
1. **URL parameter contract:** Either (a) Kojo adds `id` and `slot` parameter reading to the page so it can pre-fill member details and track conversions, OR (b) Róisín rewrites all win-back links to use `?cohort=a|b|c` and drops the member-specific ID/personalisation claims. Option (a) is preferred — it preserves the personalisation value proposition and enables campaign measurement.

**What should be fixed (non-blocking):**
2. **IntersectionObserver:** Add `IntersectionObserver`-driven CSS class toggling for DOM section crossfades (per design spec §10 FLAG 5) to replace JS-driven `style.opacity` manipulation. This improves GPU compositing and matches the designer's spec.
3. **Proof beat stagger:** Add 150ms staggered entrance delay between the three proof beats (per design spec §4.2) — currently the entire proof section fades as one unit.
4. **Marketing cohort math:** Marketing.md claims 195 total members in the sequence summary but the three cohorts sum to 185. Either clarify the 10 "2 bookings, recent" members' disposition or correct the total.

**What is strong:**
- Diagnosis: data-bound, methodical, transparent about its own discrepancies
- Brand discipline: palette, fonts, voice consistently applied across all four specialists
- GDPR hygiene: no pre-ticked consent, clear opt-outs, no shame/urgency in any copy
- No hallucinated numbers: every stat traces to the CSV
- Fallback robustness: the page converts with or without 3D, with or without WebGL, with or without motion
- One-metric focus: all four specialists name and serve the same primary metric

**Bottom line:** The campaign is 90% ship-ready. The URL parameter mismatch is the one thing that breaks the closed loop from message → page → booking → measurement. Fix that, and lift the STOP gate.

---

*Cold Verify complete. Handoff to Purple Manager for STOP gate decision.*
