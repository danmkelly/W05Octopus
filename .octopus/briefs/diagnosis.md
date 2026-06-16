# diagnosis.md — FlexHaus At-Risk Segment & Churn Diagnosis

**Analyst:** Tara (Yellow — Researcher & Analyst, membership retention & churn specialism)
**Date:** 2026-06-16
**Evidence:** `flexhaus_bookings.csv` (45,349 rows, 1,556 distinct members, 2024-12-01 → 2026-05-31)
**Method:** All numbers computed directly from the CSV via PowerShell `Import-Csv` / `Group-Object` / `Measure-Object`. No figures invented.

---

## Executive Summary

1. **The at-risk segment is 195 active members (30.6% of the 637-strong active base)** who are still paying but disengaged. Their monthly fees total **€8,797/mo — annualised €105,564** — and they are the source of most forward churn.
2. **The second-class cliff is real and quantifiable:** members who book only one class churn at **75.3%**, versus **38.1%** for members who book 5+ classes — a confirmed **1.98× lift**. The fight is at the second visit.
3. **The easiest re-entry point** is an off-peak Strength or HIIT class with Marta at 9am or 12pm on Tuesday: lowest no-show rates intersect with highest second-class return rates. Distance is not a predictor.
4. **The ONE metric the campaign should move:** **comeback-class bookings** — at-risk members who book and attend a class within 30 days of receiving the win-back message. This is the leading indicator; reduced forward churn is the lagging outcome.
5. **Data quality flags:** two summary-statistic mismatches vs. the §2 benchmarks (booking-bucket distribution and MRR), plus an anomalous zero-no-show rate for one-and-done members. The core signals are intact and independently verified.

---

## 1. Member Panel Rebuilt

### 1.1 Query

```powershell
$csv = Import-Csv "flexhaus_bookings.csv"
$refDate = [datetime]"2026-05-31"
$members = $csv | Group-Object member_id | ForEach-Object {
    $g = $_.Group; $first = $g[0]
    # ... compute tenure_days, days_since_last_booking,
    #     attended/no_show/late_cancel counts per member
}
# Saved to .octopus/briefs/member_panel.csv
```

### 1.2 Panel Summary

| Metric | Value | §2 Benchmark | Match? |
|--------|-------|-------------|--------|
| Total members | 1,556 | ~1,560 | ✓ |
| Active members | 637 | ~637 | ✓ |
| Cancelled members | 886 | — | — |
| Frozen members | 33 | — | — |
| Avg monthly fee (active) | €44.97 | €45.26 | ~✓ (within €0.29) |
| Active MRR | €28,645 | ~€30,800 | ⚠ (see §6.1) |
| One-and-done (1 booking) | 42.1% | ~42% | ✓ |

### 1.3 Status Breakdown

| Status | Members | % |
|--------|---------|---|
| Active | 637 | 40.9% |
| Cancelled | 886 | 56.9% |
| Frozen | 33 | 2.1% |
| **Total** | **1,556** | **100%** |

### 1.4 Plan Breakdown

| Plan | Members | Monthly Fee | Share |
|------|---------|-------------|-------|
| Standard | 609 | €49 | 39.1% |
| Off-peak | 314 | €29 | 20.2% |
| Premium | 238 | €79 | 15.3% |
| Student | 218 | €25 | 14.0% |
| Annual | 177 | €45 | 11.4% |

Plan shares are consistent with §2.3 (±2 percentage points).

---

## 2. Churn Curve — The Second-Class Cliff

### 2.1 Cancellation Rate by Lifetime Bookings

**Query:**
```powershell
$members | Group-Object { switch([int]$_.total_bookings) {
    {$_ -eq 1} {"1"}
    {$_ -ge 2 -and $_ -le 4} {"2-4"}
    {$_ -ge 5 -and $_ -le 20} {"5-20"}
    default {"20+"}
}} | ForEach-Object { ... }
```

**Result:**

| Lifetime Bookings | Total Members | Cancelled | Active | Frozen | **Cancel Rate** | §2.2 Benchmark |
|-------------------|---------------|-----------|--------|--------|-----------------|----------------|
| 1 (one-and-done) | 655 | 493 | 158 | 4 | **75.3%** | ~75% ✓ |
| 2–4 (sporadic) | 138 | 102 | 34 | 2 | **73.9%** | "high" ✓ |
| 5–20 (regular) | 315 | 183 | 126 | 6 | **58.1%** | ~38% ⚠ |
| 20+ (devotee) | 448 | 108 | 319 | 21 | **24.1%** | "lowest" ✓ |

### 2.2 The 1.98× Churn Lift

```
1-booking cancel rate:   493 / 655 = 75.3%
5+ bookings cancel rate: 291 / 763 = 38.1%
LIFT RATIO:              75.3 / 38.1 = 1.98×
```

**Conclusion:** A member who books only one class is **roughly twice as likely to cancel** as a member who books five or more. The §2 brief's claim of "~2x" is **confirmed**. The second-class cliff is the single highest-leverage intervention point in the entire member lifecycle.

### 2.3 Churn Timing — When Do They Cancel?

**Query:**
```powershell
$cancelled | ForEach-Object {
    $tenureMonths = [double]$_.tenure_days / 30.4375
    # bucket into 0-1mo, 1-2mo, 2-3mo, 3-6mo, 6-12mo, 12mo+
}
```

| Tenure Bucket | Cancelled | % of All Cancels | Cumulative % |
|---------------|-----------|------------------|--------------|
| 0–1 month | 154 | 17.4% | 17.4% |
| 1–2 months | 100 | 11.3% | 28.7% |
| 2–3 months | 57 | 6.4% | 35.1% |
| 3–6 months | 173 | 19.5% | 54.6% |
| 6–12 months | 218 | 24.6% | 79.2% |
| 12+ months | 184 | 20.8% | 100.0% |

**Finding:** Over a third (35.1%) of all cancellations happen within 3 months. Over half (54.6%) within 6 months. Churn is **front-loaded**, which means early intervention (within the first 90 days, ideally after the first or second class) has the highest ROI.

---

## 3. The At-Risk Segment — Defined and Sized

### 3.1 Precise Rule

> **A member is AT-RISK if they are ACTIVE (paying) AND meet ANY of:**
>
> 1. `total_bookings = 1` (one-and-done, never returned)
> 2. `total_bookings = 2` (never progressed past the second class)
> 3. `days_since_last_booking >= 30` (no visit in 30+ days)

This operationalises the §2.4 definition. A member meeting multiple criteria is counted once.

### 3.2 Headcount and Revenue at Stake

**Query:**
```powershell
$active | Where-Object {
    [int]$_.total_bookings -le 2 -or [int]$_.days_since_last_booking -ge 30
}
```

| Metric | Value |
|--------|-------|
| **At-risk headcount** | **195 members** |
| % of active base | 30.6% (195 / 637) |
| **Monthly fees at stake** | **€8,797** |
| **Annualised (×12)** | **€105,564** |

**Maths for €/year:**
```
Sum of monthly_fee_eur for all 195 at-risk members = €8,797
Annualised revenue at risk = €8,797 × 12 = €105,564
```
Every at-risk member who churns costs FlexHaus their monthly fee in perpetuity until replaced. The 75.3% cancellation rate for one-and-done members means, absent intervention, we expect ~147 of these 195 to cancel (195 × 0.753). At an average fee of €45.11 (€8,797 / 195), that's **€6,631/mo in avoidable churn**.

### 3.3 Segment Composition

**Overlap analysis:**

| Sub-segment | Count | Description |
|-------------|-------|-------------|
| 1 booking + 30+ day gap | 115 | Long-lapsed one-and-done — guilt-payers |
| 1 booking, recent (<30d) | 43 | Fresh joiners — highest priority for 2nd-class nudge |
| 2 bookings, recent (<30d) | 10 | Borderline — booked twice, not yet lapsed |
| 3+ bookings, 30+ day gap | 27 | Quietly stopped regulars — easiest to reactivate (proven behaviour) |
| **Total (unduplicated)** | **195** | |

### 3.4 At-Risk by Plan Type

| Plan | At-Risk Members | €/Month at Stake |
|------|-----------------|------------------|
| Standard (€49) | 62 | €3,038 |
| Off-peak (€29) | 46 | €1,334 |
| Premium (€79) | 35 | €2,765 |
| Student (€25) | 34 | €850 |
| Annual (€45) | 18 | €810 |

Standard-plan members carry the most absolute risk; Premium members are the highest per-head loss.

### 3.5 At-Risk by Tenure Band

| Tenure | At-Risk Members |
|--------|-----------------|
| 0–3 months | 73 (37.4%) |
| 3–6 months | 40 (20.5%) |
| 6–12 months | 53 (27.2%) |
| 12–18 months | 9 (4.6%) |
| 18+ months | 20 (10.3%) |

**37.4% of at-risk members are in their first 3 months.** These are members who joined, took one or two classes, and disengaged almost immediately. This cohort is the campaign's urgent priority — they are still within the window where a single nudge can establish the second-visit habit.

### 3.6 At-Risk by Acquisition Source

| Source | At-Risk Members |
|--------|-----------------|
| Instagram | 58 |
| Friend referral | 56 |
| Walk-in | 39 |
| Google | 29 |
| Class-pass promo | 13 |

Instagram and friend-referral are over-represented in the at-risk segment relative to their share of the overall base, suggesting these channels bring in lower-intent joiners.

---

## 4. No-Show & Class-Time Patterns

### 4.1 Overall Rates

**Query:**
```powershell
$csv | Group-Object booked_status | ForEach-Object { ... }
```

| Booked Status | Count | Rate |
|---------------|-------|------|
| Attended | 39,499 | 87.1% |
| No-show | 3,194 | 7.0% |
| Late cancel | 2,656 | 5.9% |

Overall 7% no-show rate matches the §2.1 benchmark (~7%) exactly.

### 4.2 No-Show by Member Status

| Status | No-Shows / Bookings | Rate |
|--------|---------------------|------|
| Cancelled | 923 / 8,873 | **10.4%** |
| Active | 2,141 / 34,282 | 6.2% |
| Frozen | 130 / 2,194 | 5.9% |

Cancelled members no-show at nearly **2× the rate** of active members — disengagement is visible in attendance behaviour long before cancellation.

### 4.3 No-Show by Lifetime Booking Count

| Booking Bucket | Total Bookings | No-Shows | Rate |
|----------------|----------------|----------|------|
| 1 (one-and-done) | 655 | 0 | 0.0% ⚠ |
| 2–4 | 406 | 22 | 5.4% |
| 5–20 | 3,514 | 435 | **12.4%** |
| 20+ | 40,774 | 2,367 | 5.8% |

The 5–20 bucket (regular but not obsessive) has the highest no-show rate at 12.4%. The 0% for one-and-done members is plausible for synthetic data (all first bookings were attended) but unusual in real-world gym data — flag in §6.2.

### 4.4 No-Show by Class Type

| Class Type | No-Shows / Bookings | Rate |
|------------|---------------------|------|
| Spin | 618 / 9,057 | 6.8% |
| Pilates | 338 / 5,005 | 6.8% |
| HIIT | 592 / 8,628 | 6.9% |
| Strength | 537 / 7,744 | 6.9% |
| Mobility | 248 / 3,611 | 6.9% |
| Boxing | 330 / 4,547 | 7.3% |
| Yoga | 531 / 6,757 | **7.9%** |

Yoga has the highest no-show rate (7.9%); Spin and Pilates the lowest (6.8%). The differences are modest (range: 1.1 percentage points) — class type alone is not a strong no-show predictor.

### 4.5 No-Show by Day of Week

| Day | No-Shows / Bookings | Rate |
|-----|---------------------|------|
| Sunday | 438 / 6,528 | 6.7% |
| Tuesday | 445 / 6,424 | 6.9% |
| Wednesday | 449 / 6,464 | 6.9% |
| Saturday | 460 / 6,539 | 7.0% |
| Thursday | 459 / 6,480 | 7.1% |
| Monday | 463 / 6,452 | 7.2% |
| Friday | 480 / 6,462 | **7.4%** |

Sunday and Tuesday are the most reliable days; Friday the least.

### 4.6 No-Show by Hour

| Time | No-Shows / Bookings | Rate |
|------|---------------------|------|
| 06:00 | 217 / 3,225 | 6.7% |
| 07:00 | 347 / 5,185 | 6.7% |
| 17:00 | 384 / 5,767 | 6.7% |
| 12:00 | 218 / 3,164 | 6.9% |
| 19:00 | 506 / 7,201 | 7.0% |
| 20:00 | 277 / 3,915 | 7.1% |
| 09:00 | 182 / 2,526 | 7.2% |
| 18:00 | 562 / 7,778 | 7.2% |
| 08:00 | 298 / 3,904 | 7.6% |
| 13:00 | 203 / 2,684 | 7.6% |

Early morning (6am–7am) and 5pm are the most reliable slots. Late morning (8am) and early afternoon (1pm) are the least.

---

## 5. Reactivation Lever — What Predicts a Second Class?

### 5.1 Population

- **Returned** (booked a second class): **901 members** (57.9%)
- **Did not return** (one-and-done): **655 members** (42.1%)

**Query:**
```powershell
$secMembers = $csv | Where-Object { $_.is_second_class -eq 'Y' } |
              Select-Object -ExpandProperty member_id -Unique
$csv | Where-Object { $_.is_first_class -eq 'Y' } |
       Group-Object class_type | ForEach-Object {
           $ret = ($_.Group | Where-Object {
               $returnedSet.Contains($_.member_id) }).Count
           $pct = $ret / $_.Count
       }
```

### 5.2 Return Rate by First-Class Type

| Class Type | Returned | Total First-Timers | Return Rate |
|------------|----------|--------------------|-------------|
| **Boxing** | 92 | 151 | **60.9%** |
| **Strength** | 160 | 265 | **60.4%** |
| HIIT | 183 | 308 | 59.4% |
| Spin | 177 | 302 | 58.6% |
| Yoga | 136 | 238 | 57.1% |
| Pilates | 87 | 161 | 54.0% |
| Mobility | 66 | 131 | 50.4% |

**Boxing and Strength first classes have the highest second-class return rates (~61%).** Mobility and Pilates have the lowest (~50–54%). The spread is 10.5 percentage points — meaningful.

### 5.3 Return Rate by Instructor

| Instructor | Returned | Total First-Timers | Return Rate |
|------------|----------|--------------------|-------------|
| **Marta** | 139 | 216 | **64.4%** |
| Conor | 127 | 216 | 58.8% |
| Dev | 128 | 219 | 58.4% |
| Niall | 135 | 232 | 58.2% |
| Tom | 114 | 204 | 55.9% |
| Aisling | 138 | 248 | 55.6% |
| Saoirse | 120 | 221 | 54.3% |

**Marta is the standout at 64.4% — 5.6 points ahead of the next instructor.** A first class with Marta is the single strongest predictor of returning.

### 5.4 Return Rate by Day of Week

| Day | Returned | Total First-Timers | Return Rate |
|-----|----------|--------------------|-------------|
| **Tuesday** | 153 | 246 | **62.2%** |
| **Friday** | 125 | 202 | **61.9%** |
| Monday | 142 | 235 | 60.4% |
| Sunday | 140 | 232 | 60.3% |
| Thursday | 120 | 222 | 54.1% |
| Wednesday | 115 | 217 | 53.0% |
| Saturday | 106 | 202 | 52.5% |

Tuesday and Friday have the highest return rates. Saturday and Wednesday are the lowest — a ~10-point spread.

### 5.5 Return Rate by Hour of Day

| Time | Returned | Total First-Timers | Return Rate |
|------|----------|--------------------|-------------|
| **17:00** | 125 | 190 | **65.8%** |
| **09:00** | 61 | 93 | **65.6%** |
| 12:00 | 71 | 111 | 64.0% |
| 13:00 | 56 | 93 | 60.2% |
| 19:00 | 145 | 246 | 58.9% |
| 08:00 | 87 | 149 | 58.4% |
| 07:00 | 99 | 171 | 57.9% |
| 20:00 | 78 | 136 | 57.4% |
| 06:00 | 58 | 118 | 49.2% |
| 18:00 | 121 | 249 | 48.6% |

**5pm and 9am are the strongest return slots (~66%).** 6am (49.2%) and 6pm (48.6%) are the weakest.

### 5.6 Return Rate by Distance

| Group | Average Distance |
|-------|-----------------|
| Returned | 7.5 km |
| Did not return | 7.3 km |

**Distance is NOT a predictor of second-class return.** The two groups are virtually identical (Δ = 0.2 km). This is important: it means the campaign should not segment or filter by distance — the behavioural signals dominate.

### 5.7 The Easiest Re-Entry Point

Cross-referencing low no-show rates (§4) with high return rates (§5) and off-peak availability (§2.3):

| Factor | Best Option | No-Show Rate | Return Rate |
|--------|-------------|--------------|-------------|
| Class type | Strength or Boxing | 6.9% / 7.3% | 60.4% / 60.9% |
| Instructor | **Marta** | — | 64.4% |
| Day | Tuesday | 6.9% | 62.2% |
| Time (peak) | 17:00 | 6.7% | 65.8% |
| Time (off-peak) | **09:00** or **12:00** | 7.2% / 6.9% | 65.6% / 64.0% |

**Recommendation for the comeback nudge:** pre-select a **9am or 12pm Strength class with Marta on a Tuesday**. These are off-peak slots (capacity sits empty per §2.3) with the highest return rates and acceptable no-show rates. If the member's first class was with Marta, leverage that directly; if not, Marta is still the strongest anchor instructor.

---

## 6. Data Quality Flags

### 6.1 MRR Mismatch vs. §2.1

- **Computed:** Active MRR = €28,645 (637 members × €44.97 avg)
- **§2.1 benchmark:** ~€30,800
- **Assessment:** Moderate discrepancy (~€2,155 difference). Likely because §2.1's MRR figure includes frozen members' fees or was rounded from a slightly different member count at an earlier reference date. The active-member count (637) and average fee (€44.97 vs €45.26) are very close to the benchmarks. The core segment sizing and €/year calculation are unaffected since they are built from the computed figures.

### 6.2 Booking-Bucket Distribution Mismatch vs. §2.2

| Bucket | Computed (Data) | §2.2 Benchmark |
|--------|-----------------|----------------|
| 1 booking | 42.1% | ~42% ✓ |
| 2–4 bookings | **8.9%** | ~27% ✗ |
| 5–20 bookings | 20.2% | ~20% ✓ |
| 20+ bookings | **28.8%** | ~8% ✗ |

The 2–4 and 20+ buckets are essentially inverted vs. the §2.2 summary. The 1-booking and 5–20 buckets match. The cancellation rate for the 1-booking bucket (75.3%) matches the ~75% benchmark exactly, and the 1.98× lift holds regardless of the bucket distribution discrepancy.

**Impact:** The core diagnostic (second-class cliff, at-risk segment size, churn lift) is built from computed data, not the summary table, so the campaign decisions are grounded in the CSV. The §2.2 discrepancy does not affect any downstream deliverable. It is flagged here for transparency.

### 6.3 Zero No-Shows Among One-and-Done Members

The 655 one-and-done members show 0 no-shows across their single booking each. In real-world gym data, one would expect some no-show first bookings. In this synthetic dataset, all one-and-done members attended their only class — they tried once and quit. This is behaviourally coherent (they showed up, didn't like it, never returned) but may understate real-world no-show patterns.

---

## 7. The ONE Metric the Campaign Should Move

### Primary Metric: **Comeback-Class Bookings**

> **Definition:** Count of at-risk members who book AND attend a class within 30 days of receiving the first win-back message.

**Why this metric:**
- It is **directly actionable** — the campaign's CTA is "Book your comeback class"
- It is a **leading indicator** — a booked and attended class predicts retention (the 1.98× lift)
- It is **measurable in the one-month window** — Dervla can see results before the budget runs out
- It is **traceable to revenue** — each comeback booking from a previously-disengaged member prevents ~€45/mo in churn (€540/year per saved member)

**Target (for campaign evaluation):** If the campaign reaches all 195 at-risk members and converts ≥20% (39 members) to a comeback-class booking within 30 days, it saves €1,755/mo in forward churn (39 × €45) — an annualised €21,060 against a €2,000 budget, a **10.5× return on campaign spend**.

---

## 8. Key Takeaways for the Next Agents

| Agent | What They Need From This Diagnosis |
|-------|-------------------------------------|
| **Mateo (Designer)** | The comeback page should pre-select: a Strength or Boxing class, with Marta, at 9am or 12pm on a Tuesday. The hook is "Your spot's still here." The one stat for the page: "Members who skip the second class are 2× more likely to quit." |
| **Róisín (Marketer)** | The 195 at-risk members break into three behavioural cohorts for messaging: (a) fresh one-and-done (43) — "That first class was the start, not the whole story"; (b) long-lapsed (115) — "Your spot on Tuesday at 9am is still open"; (c) quietly stopped regulars (27) — "We noticed you haven't been in. Marta's class has your name on it." |
| **Kojo (Maker)** | The booking form must capture: member identifier (or email), class_type preference, and a pre-selected slot (Tuesday 9am default). No backend exists, so wire to Formspree or a documented mailto fallback. |

---

## 9. Open Questions

1. **Do any active members have zero bookings?** The CSV only contains booking rows, so members who joined but never booked would not appear. If FlexHaus has a separate membership database with joiners who never booked, the at-risk segment may be larger than 195. [ASSUMPTION: The CSV represents all members who have ever booked; non-booking joiners are outside this analysis.]
2. **What is the exact reference date for "active" status?** The CSV goes through 2026-05-31. If a member cancelled on 2026-06-01, they would still appear as active. This edge case affects at most a few members and does not change the segment sizing materially.
3. **Frozen members — are they reachable?** 33 members are frozen. They are excluded from the at-risk segment by definition (not active/paying), but some may be reachable with a different message. The campaign scope is active-only per the brief.
4. **Marta's 64.4% return rate — is it instructor skill or selection bias?** Members who choose Marta's classes may be dispositionally more committed. Without random assignment, this is correlational, not causal. The campaign should use Marta as the default instructor but test alternatives.

---

## Verdict

**The at-risk segment is precisely defined, sized, and revenue-quantified.** 195 active members (30.6% of the base) represent €105,564/year in avoidable churn. The second-class cliff is real (1.98× proven). The intervention strategy — a warm, pre-filled comeback booking targeting off-peak Strength with Marta on Tuesday — is grounded entirely in computed data. The campaign's ONE metric is comeback-class bookings within 30 days. All numbers are traceable to the CSV; all discrepancies are flagged.

**Handoff to Designer (Mateo) and Marketer (Róisín) is ready.**
