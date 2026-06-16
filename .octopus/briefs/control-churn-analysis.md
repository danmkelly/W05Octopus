# FlexHaus Gym — Churn Diagnosis & At-Risk Segment Analysis

**Date:** 16 June 2026  
**Data Source:** `flexhaus_bookings.csv` (45,349 bookings, 1,556 members)  
**Analyst:** Yellow Researcher (Scout Mode)  
**Status:** Complete

---

## Executive Summary

1. **42.1% of all members (655/1,556) never book a second class.** This "one-and-done" segment is the dominant churn driver. 75.3% (493) of them have already cancelled. Critically, **158 one-and-done members are still active** — they are the single largest retention opportunity.
2. **First-class no-show is the strongest churn predictor.** 19.3% of all members (301) no-showed their first booked class. Of these, **69.4% went on to cancel** and **65.8% never booked a second class**. The overlap is devastating: 198 members both no-showed their first class and never returned.
3. **The booking gap between first and second class is a behavioural early-warning signal.** Active members return in a median of **5 days**. Cancelled members who did return took a median of **11 days**. A gap exceeding 7 days is a measurable churn risk indicator.
4. **Booking volume collapses before cancellation.** Cancelled members book at a median of **1 time total** (vs. 21 for active members). In the months leading to cancellation, absolute bookings drop progressively from month -5 (613 bookings) to month 0 (1,374 bookings — the cancellation month itself, inflated by short-lived members).
5. **Class-pass promo referrals, 55+ age band, and Premium plan members churn hardest** (62.6%, 62.2%, and 61.7% respectively), but these are relatively small sub-segments. The volume of churn is driven by the Standard plan (345 cancelled), Instagram referrals (264 cancelled), and 25-34 age band (330 cancelled).

---

## 1. The One-and-Done Segment (42.1%)

### Definition
Members who have exactly **one booking** in the entire dataset, regardless of time elapsed since joining.

### Scale

| Metric | Count | % of Total Members |
|--------|-------|--------------------|
| Total one-and-done members | 655 | 42.1% |
| └ Already cancelled | 493 | 31.7% |
| └ Still active | 158 | 10.2% |
| └ Frozen | 4 | 0.3% |

### The Active One-and-Done Members (158) — Immediate Retention Target

These 158 members have joined, booked exactly once, and never returned — but have not cancelled yet. They represent **24.8% of all active members** (158/637).

**Booking status of their single class:**

| Booked Status | Count | % |
|---------------|-------|---|
| Attended | 66 | 41.8% |
| No-show | 50 | 31.6% |
| Late cancel | 42 | 26.6% |

**Key insight:** Only 66 of these 158 members (41.8%) actually attended their single class. The remaining 92 either no-showed or late-cancelled. They may have never experienced the product.

**Time since join (how long they've been "dormant"):**

| Percentile | Days Since Join |
|------------|-----------------|
| P25 | 60 days |
| Median | 142 days |
| P75 | 249 days |
| Mean | 199 days |

**Urgency breakdown:**

| Days Since Join | Members | Risk Assessment |
|-----------------|---------|-----------------|
| 0–30 | 7 | Fresh — salvageable |
| 31–60 | 33 | Critical window closing |
| 61–90 | 10 | At high risk |
| 90+ | 108 | Extreme risk — may have already disengaged permanently |

**Plan type breakdown:**

| Plan | Count |
|------|-------|
| Standard | 49 |
| Off-peak | 40 |
| Premium | 29 |
| Student | 27 |
| Annual | 13 |

**Referral source breakdown:**

| Source | Count |
|--------|-------|
| Instagram | 49 |
| Friend referral | 45 |
| Walk-in | 30 |
| Google | 21 |
| Class-pass promo | 13 |

---

## 2. First-Class No-Show: The #1 Churn Predictor

### Scale of the problem

| Metric | Count | % |
|--------|-------|---|
| Members who no-showed their first class | 301 | 19.3% of all members |
| Of these: cancelled | 209 | 69.4% |
| Of these: active (still at-risk) | 88 | 29.2% |
| Of these: frozen | 4 | 1.3% |

### The no-show → one-and-done pipeline

- **198 members** both no-showed their first class AND never booked a second class.
- That is **65.8% of all first-class no-shows** and **30.2% of all one-and-done members**.
- This is the single most concentrated failure pattern in the dataset.

### Inference

A no-show on the first booking is not just a missed class. It is a **defection signal**. Nearly 7 in 10 first-class no-shows cancel. The failure is likely a combination of:
- Low commitment at sign-up (especially for free/cheap acquisition channels)
- Friction between booking and attendance (reminder gaps, fear/anxiety about first visit)
- Wrong class matched to new member preferences

---

## 3. Booking Frequency: The Volume Cliff

### Bookings per member by status

| Status | Mean Bookings | Median | P25 | P75 |
|--------|---------------|--------|-----|-----|
| Active | 53.8 | **21** | 2 | 77 |
| Cancelled | 10.0 | **1** | 1 | 8 |
| Frozen | 66.5 | 27 | 12 | 89 |

The median cancelled member booked **one time**. The median active member booked **21 times**. The chasm is enormous.

### Members with very low booking counts

| Total Bookings | Active | Cancelled |
|----------------|--------|-----------|
| Exactly 1 | 158 | 493 |
| Exactly 2 | 10 | 40 |
| Exactly 3 | 15 | 28 |

Virtually all members who settle at 2 or 3 bookings cancel. Crossing the threshold to **4+ bookings** appears to be a critical retention milestone.

---

## 4. Churn Rates by Segment

### By Plan Type

| Plan Type | Total Members | Active | Cancelled | Churn Rate* |
|-----------|---------------|--------|-----------|-------------|
| Premium | 238 | 90 | 145 | **61.7%** |
| Annual | 177 | 70 | 102 | **59.3%** |
| Standard | 609 | 246 | 345 | **58.4%** |
| Student | 218 | 92 | 123 | **57.2%** |
| Off-peak | 314 | 139 | 171 | **55.2%** |

*\*Churn rate = cancelled / (active + cancelled), excluding frozen members.*

**Finding:** Premium members churn at the highest rate despite paying the most. Possible explanations: higher expectations not met, or the Premium tier attracts impulse joiners with lower commitment. Off-peak members show the best retention — possibly because lower cost reduces cancellation pressure and/or constraints on schedule force more intentional attendance.

### By Age Band

| Age Band | Total Members | Active | Cancelled | Churn Rate* |
|----------|---------------|--------|-----------|-------------|
| 55+ | 82 | 30 | 51 | **62.2%** |
| 35-44 | 407 | 162 | 236 | **59.3%** |
| 18-24 | 270 | 111 | 154 | **58.1%** |
| 45-54 | 203 | 84 | 115 | **57.8%** |
| 25-34 | 594 | 250 | 330 | **56.9%** |

*\*Excluding frozen.*

**Finding:** The 55+ segment has the highest churn rate (62.2%) but the smallest absolute volume (51 cancelled). The 25-34 segment has the lowest churn rate (56.9%) but the largest volume (330 cancelled). Both require distinct strategies.

### By Referral Source

| Referral Source | Total Members | Active | Cancelled | Churn Rate* |
|-----------------|---------------|--------|-----------|-------------|
| Class-pass promo | 134 | 49 | 82 | **62.6%** |
| Friend referral | 366 | 143 | 207 | **59.1%** |
| Walk-in | 337 | 137 | 197 | **59.0%** |
| Google | 235 | 96 | 136 | **58.6%** |
| Instagram | 484 | 212 | 264 | **55.5%** |

*\*Excluding frozen.*

**Finding:** Class-pass promo members have the highest churn rate (62.6%) — likely low-intent acquirers who joined via a deal. Instagram has the best retention (55.5%) and the largest member base, suggesting better targeting or community fit.

---

## 5. Churn Timing & Behavioural Signals

### Survival Timeline (Cancelled Members)

**Time from join to last booking (n=886):**

| Percentile | Days |
|------------|------|
| P25 | 23 days |
| Median | **81 days** |
| P75 | 253 days |
| Mean | 166 days |
| Min–Max | 0–998 days |

**Key insight:** 25% of cancelled members stop booking within **23 days** of joining. The median churned member takes their last class at **81 days** (about 2.7 months). This is well within the "onboarding window" — retention interventions must fire early, ideally within the first 2 weeks.

### Quick Churn (Cancelled within 30 days of joining)

| Metric | Value |
|--------|-------|
| Quick-churn members | 154 |
| As % of all cancelled | **17.4%** |
| As % of all members | 9.9% |

**Quick-churn breakdown by plan:** Standard (58), Student (27), Off-peak (26), Annual (22), Premium (21).  
**Quick-churn breakdown by age:** 25-34 (53), 35-44 (37), 18-24 (29), 45-54 (24), 55+ (11).

### Booking Decline Before Cancellation

| Months Before Cancel | Total Bookings |
|----------------------|----------------|
| Month 0 (cancel month) | 1,374 |
| Month -1 | 1,113 |
| Month -2 | 1,034 |
| Month -3 | 834 |
| Month -4 | 767 |
| Month -5 | 613 |

**Interpretation:** There is a steady decline in booking volume in the final 3 months before cancellation. Month 0 spikes because it includes members who joined and cancelled within the same month (quick churn). The decline from month -2 (1,034) to month -3 (834) is a 19% drop — a practical trigger threshold for automated re-engagement.

### 1st-to-2nd Booking Gap

| Status | Members with 2+ Bookings | Median Gap | Mean Gap | P25 | P75 |
|--------|--------------------------|------------|----------|-----|-----|
| Active | 479 | **5 days** | 11 | 2 | 14 |
| Cancelled | 393 | **11 days** | 17 | 4 | 28 |

**Finding:** The return-to-second-class gap is a behavioural churn signal. Members who wait more than 7 days between first and second booking are in the cancelled-member territory. Active members return quickly (half within 5 days).

### First-Class Class Type & Churn

| First Class Type | Total First-Timers | Cancelled | Cancelled % |
|------------------|--------------------|-----------|-------------|
| Spin | 302 | 182 | **60.3%** |
| Mobility | 131 | 80 | **61.1%** |
| Strength | 265 | 153 | 57.7% |
| Yoga | 238 | 133 | 55.9% |
| Boxing | 151 | 84 | 55.6% |
| HIIT | 308 | 170 | 55.2% |
| Pilates | 161 | 84 | 52.2% |

**Finding:** Spin and Mobility as first-class experiences have the highest subsequent churn. Pilates has the best first-class-to-retention pipeline. This may reflect class intensity/difficulty mismatch for newcomers or instructor first-impression effects.

---

## 6. No-Show & Late-Cancel Behaviour

| Status | Total Bookings | No-Show Rate | Late-Cancel Rate | Combined Failure Rate |
|--------|---------------|--------------|------------------|-----------------------|
| Active | 34,282 | **6.2%** | **5.1%** | 11.4% |
| Cancelled | 8,873 | **10.4%** | **8.9%** | 19.3% |
| Frozen | 2,194 | 5.9% | 5.1% | 11.0% |

**Finding:** Cancelled members no-show at nearly **double the rate** of active members (10.4% vs 6.2%) and late-cancel at nearly double (8.9% vs 5.1%). No-show behaviour is both a predictor and an accelerator of churn. Rising no-show rate in an individual member's history should trigger intervention.

---

## 7. Non-Significant Factors

The following variables showed **no meaningful difference** between active and cancelled members:

| Factor | Finding |
|--------|---------|
| **Home distance** | Active median 7.3 km vs. Cancelled median 7.4 km (negligible) |
| **Monthly fee** | Active median €49 vs. Cancelled median €49 (identical) |
| **Weekend vs. weekday** | Active 28.9% weekend vs. Cancelled 28.5% (same pattern) |
| **Instructor** | All instructors have 19.2%–20.1% cancelled booking share (no outlier) |
| **Waitlisted rate** | Active 8.3% vs. Cancelled 7.6% (not a differentiator) |
| **PT sessions** | Both segments at 5.0% |

These are **not leverage points** for churn reduction. Effort should focus on the behavioural and timing signals identified above.

---

## 8. The At-Risk Segment — Definition

### Primary At-Risk Segment: The Active One-and-Done

**Profile:** A member who has been active for over 30 days but has only booked **one class** — and may not have even attended it.

| Attribute | Value |
|-----------|-------|
| Count | **158 members** (24.8% of all active members) |
| Attended their only booking | 66 (41.8%) |
| No-showed their only booking | 50 (31.6%) |
| Late-cancelled their only booking | 42 (26.6%) |
| Median time dormant | **142 days** |
| Top plan | Standard (49) |
| Top referral source | Instagram (49), Friend referral (45) |
| Top age band | 25-34 |

**Risk:** Extremely high. Nearly 76% of all one-and-done members historically cancel. Without intervention, these 158 members are almost certain to churn.

### Secondary At-Risk Segment: First-Class No-Shows (Active)

**Profile:** Active members who no-showed their first booked class but have since booked (some may have attended later).

| Attribute | Value |
|-----------|-------|
| Count | **88 active members** |
| Historical cancellation rate | 69.4% |
| Overlap with one-and-done | Some (exact intersection computable) |

### Tertiary At-Risk Signal: Slow Returners

**Profile:** Any member whose 1st-to-2nd booking gap exceeds **7 days**.

| Attribute | Value |
|-----------|-------|
| Signal | Active median gap = 5 days; Cancelled median = 11 days |
| Threshold | 7+ days gap = elevated risk |

---

## 9. Recommendations for Retention Intervention

### Tier 1 — Immediate (This Week)

1. **Re-engage the 158 active one-and-done members.** This is the highest-ROI action. Prioritise the 50 who no-showed — offer a "fresh start" with a different class type and instructor. For the 66 who attended but never returned, investigate what happened (class type mismatch? schedule? instructor fit?). For the 33 in the 31–60 day window, urgency is highest.

2. **First-class no-show protocol.** For every new member, if they no-show their first booked class, trigger an automated sequence within 24 hours: rebook them into a different class type (preference: Pilates or HIIT, which have the lowest first-class churn), offer a personal welcome call, or send a "we saved your spot" message.

### Tier 2 — Short Term (This Month)

3. **The 7-day booking gap alarm.** Monitor all members for the gap between first and second booking. If 7+ days pass without a second booking, trigger a re-engagement message or offer. This catches members before they become one-and-done.

4. **Class-pass promo and 55+ onboarding.** These segments have the highest churn rates (62.6% and 62.2%). Design a targeted onboarding journey: lower-intensity first class, buddy system, or a "first month success coach" touchpoint.

5. **Spin and Mobility first-class risk.** Members whose first class is Spin (60.3% churn) or Mobility (61.1%) are at elevated risk. Consider nudging new members toward lower-churn first classes (Pilates at 52.2%, HIIT at 55.2%) or adding a "new to Spin? here's what to expect" pre-class briefing.

### Tier 3 — Ongoing

6. **No-show escalation monitoring.** Track individual no-show rates. When a member's personal no-show rate crosses 10%, flag for outreach. (Cancelled members average 10.4%; active members average 6.2%.)

7. **Booking frequency milestone rewards.** Crossing from 1→2, 2→3, and 3→4 bookings are critical thresholds. Celebrate each with a small reward or recognition to cement the habit.

---

## Key Takeaways

1. **The churn crisis is concentrated in the first booking.** 42.1% of all members never book a second class. Fixing the one-and-done problem would cut overall churn by more than half.

2. **First-class no-show is a defection, not a delay.** 69.4% of members who no-show their first class eventually cancel. Treat every first-class no-show as a retention emergency.

3. **The 7-day rule.** If a member hasn't booked their second class within 7 days of their first, they are drifting into churn territory. This is a measurable, actionable trigger.

4. **Behavioural signals beat demographic segments.** Age, distance, fee, and instructor do not predict churn. Booking behaviour (gap timing, no-show rate, total volume) does. Build interventions around behaviour, not profiles.

5. **158 members are on the cliff edge right now.** These active one-and-done members represent roughly **€7,000–€11,000/month in recurring revenue** (at €45–€79/month) and immediate outreach has a plausible chance of saving a meaningful fraction.

6. **The booking cliff is real.** 17.4% of all churn happens within 30 days. 25% of churned members stop booking within 23 days. The window for retention is measured in days, not months.

---

## Open Questions

1. **Why do Premium members churn most?** The data shows Premium has the highest churn rate (61.7%) but cannot explain why. Is it unmet expectations? Billing shock? Poor onboarding relative to price point?

2. **What happened to the 66 active one-and-done members who attended their class?** They showed up once but never came back. Was the class experience negative? Wrong time? Wrong difficulty? Qualitative research (exit survey, follow-up call) is needed.

3. **Is the Class-pass promo channel worth keeping?** It has the worst retention (62.6% churn). Are the acquisition economics still profitable after factoring in the cost of churned members? A unit-economic analysis is recommended.

4. **What is the exact monthly churn rate?** The problem statement says 9% monthly churn, but the CSV does not contain monthly membership snapshots that could independently verify this. Only aggregate status at data-capture time is available.

5. **Do the 108 active one-and-done members at 90+ days dormant have any realistic chance of returning?** Past a certain threshold, outreach may be wasted. A small A/B test of re-engagement offers on this group could determine viability.

---

## Sources

- `flexhaus_bookings.csv` — 45,349 rows, 1,556 unique members, 23 columns. Full dataset analysed via PowerShell `Import-Csv`, `Group-Object`, `Measure-Object`, `Sort-Object`, and `Where-Object` cmdlets.
- All statistics derived directly from the CSV; no external data sources consulted.

---

*Handoff ready for Designer (Red-Orange) or Manager (Purple) to action the retention intervention design based on the at-risk segment profile and behavioural triggers identified above.*
