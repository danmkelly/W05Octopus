# FlexHaus Retention Challenge — Session State

## The Brief

FlexHaus is a single-location boutique gym with 600 members. Monthly churn is 9%, class attendance is patchy, and 40% of members never book a second class. Budget: €2,000 and one month.

**Mission:** Produce a member-retention plan with one working artifact.

**Deliverables:**
1. Diagnose the churn and find the at-risk segment
2. Design a retention concept
3. Build a real booking or sign-up page that includes top-notch sales copy to win members back

**Outputs required:** Two solutions — Control (simple inputs) and Real Brief (rich inputs).

## Data: `data/flexhaus_bookings.csv`

**45,349 bookings across 1,556 unique members.** No member table — member attributes are denormalized onto every booking row. You must GROUP BY `member_id` and take the latest values per member to rebuild the member view.

### Columns (23)
booking_id, member_id, join_date, member_status, cancel_date, plan_type, monthly_fee_eur, age_band, gender, referral_source, home_distance_km, booking_date, booking_time, day_of_week, is_weekend, class_type, instructor, room, booked_status, is_first_class, is_second_class, waitlisted, pt_session

### Key Distributions

**Member status (row-level):**
- active: 34,282 (75.6%)
- cancelled: 8,873 (19.6%)
- frozen: 2,194 (4.8%)

**Member status (by latest booking — per-member view):**
- cancelled: 886 (56.9%)
- active: 637 (40.9%) ← maps to the brief's "600 members"
- frozen: 33 (2.1%)

**Plan type:**
- Standard: 42.0% | Off-peak: 19.4% | Annual: 13.3% | Premium: 13.3% | Student: 12.0%

**Class type:**
- Spin: 9,057 | HIIT: 8,628 | Strength: 7,744 | Yoga: 6,757 | Pilates: 5,005 | Boxing: 4,547 | Mobility: 3,611

**Age band:**
- 25-34: 34% | 35-44: 25.3% | 18-24: 19.4% | 45-54: 17.1% | 55+: 4.2%

**Referral source:**
- Instagram: 36.2% | Friend referral: 21.4% | Walk-in: 20.8% | Google: 13.7% | Class-pass promo: 7.9%

**Booked status:**
- attended: 87.1% | no_show: 7.0% | late_cancel: 5.9%

**Instructors (7, evenly balanced ~6,500 each):**
Saoirse, Marta, Aisling, Tom, Niall, Conor, Dev

**Date range:** Dec 2024 — May 2026 (~18 months)

### Critical Churn Findings

**The 40% problem is confirmed:**
- 655 members (42.1%) have exactly 1 booking — is_second_class is never 'Y'
- This IS the "40% of members never book a second class"

**Churn timing:**
- Median lifespan (cancelled members): **81 days** from join to last booking
- Average: 166 days (skewed by long-timers)
- Half of cancelled members are gone within ~3 months

**Disengagement signals:**
- Cancelled members: **10.4% no-show** rate
- Active members: **6.2% no-show** rate
- No-shows spike before cancellation — early warning indicator

**Plan churn rates** (all cluster 55-61%, fairly uniform):
- Premium: 60.9% (highest) | Standard: 56.7% | Annual: 57.6% | Student: 56.4% | Off-peak: 54.5%

**Distance does NOT predict churn:**
- 0-2km: 56.2% cancelled
- 2-5km: 56.9% cancelled
- 5-10km: 55.4% cancelled
- 10km+: 59.2% cancelled
- The problem is behavioral, not locational.

**Waitlisted:** 8.2% of all bookings

### Bottom Line for the Tutorial
The at-risk segment is first-timers who don't book a second class. 42% of all members fall into this bucket, and half of those who eventually cancel are gone within 81 days. Any retention intervention needs to hit within the first 30 days post-signup.

## Project Structure
```
W05 Tutorial/
├── opencode.json              # loads octopus.md as instructions
├── .opencode/agents/          # 5 OpenCode subagent definitions
│   ├── researcher.md, designer.md, maker.md, marketer.md, manager.md
├── landing.html               # challenge overview with control/real brief tiles
├── control/index.html         # placeholder — simple inputs solution
├── real/index.html            # placeholder — rich inputs solution
├── data/flexhaus_bookings.csv # ~45k booking rows
└── octopus/                   # full victordelrosal/octopus source
```

## To Resume
OpenCode will auto-load octopus.md when started in this directory. The landing page is at `landing.html`. Deploy via GitHub Pages at `https://danmkelly.github.io/W05Octopus/landing.html`.
