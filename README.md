# Parametric Wind Pricing — Take-Home Exercise

Thanks for making it through the first round. This exercise mirrors the kind of
work you'd do on our parametric pricing platform: take a modelled event set,
design a trigger, price the structure, and be honest about its weaknesses.

We care far more about **judgment, correctness, and how you reason about basis
risk** than about volume of code. A focused, well-structured, well-argued
submission beats an exhaustive one.

---

## How to take this exercise

1. Click **“Use this template” → “Create a new repository”** at the top of this
   page (don't fork). Make **your** repo **private**.
2. Add **`waswate`** as a collaborator so we
   can see your work.
3. Do the exercise in that repo. **Work the way you normally would** — we review
   the commit history and repo hygiene as part of the assessment, so treat it like
   real work, not a single end-of-day dump.
4. When you're done, message us your repo link. See **Submission** below.

---

## Logistics

- **Effort target: ~6 focused hours.** You have **one week** from receipt. Please
  **do not gold-plate** — if you're well past 6 hours, stop and write up what
  you'd do next instead. Prioritisation is part of what we're assessing.
- **AI tools are allowed and encouraged.** Use whatever you'd use on the job. The
  one requirement: include a short **`AI_USAGE.md`** noting what you used each
  tool for, and which design decisions are your own. We'll talk through your
  choices live — so own them.
- **Deliverable: this repo,** with runnable code, a few tests, and a short memo.
  **Not a single notebook.** We want to see how you'd structure pricing logic that
  has to live in a platform — the structure is yours to design.
- **Follow-up:** a **45-minute live code review**. We'll walk your repo together
  and make one change to it on the call.

---

## The scenario

A notional SE-US coastal property portfolio (8 sites, ~\$300m total insured
value) is to be covered by a **parametric wind** structure.

The **parametric index** is the **1-minute sustained wind speed (mph) at a single
fixed reference station** near the portfolio centroid. The structure has a
**per-occurrence limit of \$10,000,000**. Your job is to design the payout
function and price the cover.

---

## Data provided (`/data`)

> Real-world note: this data has **not** been cleaned. Validate it before you
> trust it.

**`exposure.csv`** — the portfolio.
| column | meaning |
|---|---|
| `location_id`, `location_name` | site identifier |
| `latitude`, `longitude` | site coordinates |
| `tiv_usd` | total insured value at the site |
| `construction_class` | masonry / wood-frame |

**`event_set.csv`** — a modelled stochastic catalogue spanning **10,000
simulated years** (a year-loss-table style output).
| column | meaning |
|---|---|
| `year` | simulation year (1–10,000) |
| `event_id` | unique event identifier |
| `station_wind_mph` | **the parametric index**: sustained wind at the reference station |
| `ground_up_loss_usd` | modelled ground-up portfolio loss for that event |

Years with no events simply have no rows. The index drives the trigger; the
ground-up loss is what actually happened to the portfolio (you'll need both for
basis risk).

**`historical_losses.csv`** — an observed sample covering **25 years (1999–2023
inclusive)**. Same columns. Note that **several of those 25 years had no events**
and therefore appear in no rows — the experience period is still 25 years.

---

## Tasks

### A. Data ingestion & quality control
Load and validate the event set. Identify and handle any data-quality problems
before pricing. Document what you found and how you treated each issue (and why).

### B. Experience / burning-cost view
From `historical_losses.csv`, produce an experience-based expected annual loss to
the portfolio. Comment on how much weight you'd put on this number and why.

### C. Trigger & payout design *(core)*
Design a payout function on the index — binary, stepped, or continuous, your
call — against the \$10m occurrence limit. **Justify the structure you chose.**
Then compute per-event and per-year payouts across the full 10,000-year set.
This part is deliberately under-specified; your design choices are the point.

### D. EP curves, AAL & layer pricing
1. Build **OEP and AEP** curves for your parametric payout. Report the
   structure's **AAL** (expected annual payout) and verify it equals the area
   under the AEP exceedance curve.
2. *Calibration:* using the provided **modelled ground-up annual-aggregate
   losses**, price a **\$10m xs \$15m annual-aggregate layer** — report the
   expected loss to the layer, then apply a stated risk load to reach a technical
   premium.

### E. Basis risk
Quantify the basis risk between your parametric **payout** and the modelled
**ground-up loss** (e.g. shortfall when the portfolio is hit hard but the index
barely moves; over-payment when the index fires on a low-loss event). Report one
or two basis-risk metrics. Then **describe** — you don't need to implement — how
you'd reduce it if you had more time. (We'll dig into optimisation on the call.)

### F. One-page memo
A one-page memo to an underwriter: key assumptions, recommended structure,
indicated technical price, the main risks and limitations of your approach, and
what you'd do with more time.

---

## Submission
Open a **pull request** in your repo titled `Submission` (a single PR collecting
your work is fine), or just confirm your default branch is ready and send us the
link. Make sure the repo contains:

- [ ] Reproducible run (a README or script that produces your headline numbers)
- [ ] A few tests around the pricing logic
- [ ] `AI_USAGE.md`
- [ ] One-page memo (`MEMO.md` or PDF)

## What we're assessing
Domain judgment (especially C and E), data skepticism (A), correct EP/AAL/layer
mechanics (D), clean and testable code structure, clarity of the memo, sensible
prioritisation within the 6 hours, how you worked with AI tools, and how you use
git (commit history and repo hygiene).
