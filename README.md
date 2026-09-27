# Retail Distribution Portfolio — Account Health & Net Retention Analysis

**A simulated Customer Success Operations case study: 50 retail distributor accounts, $10.1M ARR.**

**Domain:** B2B SaaS — Sales Force Automation (SFA) for retail distribution. The account base modelled here is the FMCG/CPG secondary-sales channel: distributors licensing field-rep seats for beat planning, in-outlet order capture and retail execution tracking.

> **Note:** To respect B2B SaaS confidentiality, this project utilizes a synthetic dataset modeling typical retail distribution telemetry.

---

## What this repository contains

| File | Purpose |
|---|---|
| [`data/raw_portfolio_telemetry.csv`](data/raw_portfolio_telemetry.csv) | Raw account telemetry — 50 distributors, ARR, licensed vs. active field reps, order volume trend, support load, sponsor status |
| [`documentation/excel_health_scoring_model.md`](documentation/excel_health_scoring_model.md) | The scoring methodology — weighted 0–100 health formula, `IF`-based tiering, and the full NRR / GRR financial model with copy-pasteable Excel formulas |
| [`playbooks/adoption_risk_intervention.md`](playbooks/adoption_risk_intervention.md) | Gainsight-style intervention playbook for Yellow accounts with low field rep adoption, including the CSM email template |

---

## Executive summary

This portfolio is **revenue-stable but adoption-fragile.**

Net Revenue Retention lands at **97.1%** — close enough to par that a dashboard-level read would call the book healthy. It is not. That figure is being propped up by an 18% expansion uplift concentrated in 22 Green accounts. Underneath it, Gross Revenue Retention is **88.5%**, meaning the portfolio loses more than 11 points of revenue to churn and contraction every cycle and buys it back through upsell. That is a treadmill, not retention.

The structural problem is adoption. Across 8,411 licensed field reps, portfolio-weighted 30-day adoption is **68.5%** — roughly **2,650 licensed reps are dormant**. Because an SFA platform earns its value at the point of sale, on the beat, dormant licences are not idle capacity; they are unbilled invisibility. **22 accounts holding $4.38M in ARR (43.2% of the book) sit below the 70% adoption floor.**

The clearest actionable pattern in the data is political, not technical. Accounts with an engaged executive sponsor average **80.2% adoption**; accounts with a passive, absent or departed sponsor average **58.7%** — a **21.5-point gap**. Sponsorship coverage, not product capability, is the dominant variable separating Green from Red. Every account in the modelled churn cohort had both an uncovered sponsor and collapsed field adoption.

**Concentration risk compounds this.** $5.29M — 52.2% of total ARR — sits in Yellow and Red accounts, and the five largest at-risk accounts alone carry $2.07M. This book cannot be managed at a uniform cadence; it needs to be triaged by revenue-weighted risk.

---

## Portfolio at a glance

| Metric | Value |
|---|---|
| Accounts under management | 50 |
| Total ARR | $10,126,000 |
| Average health score | 65.9 / 100 |
| Licensed field reps | 8,411 |
| Portfolio-weighted rep adoption | 68.5% |
| Accounts below 70% adoption | 22 ($4.38M ARR) |
| Accounts with negative order trend | 17 |
| Accounts with no covered exec sponsor | 10 |

### Health tier distribution

| Tier | Accounts | ARR | % of ARR | Avg adoption |
|---|---|---|---|---|
| 🟢 Green (72–100) | 22 | $4,836,000 | 47.8% | 81.5% |
| 🟡 Yellow (52–71.9) | 16 | $2,383,000 | 23.5% | 68.7% |
| 🔴 Red (0–51.9) | 12 | $2,907,000 | 28.7% | 44.8% |

---

## Financial summary — Net Revenue Retention

Measured on a cohort basis across the 50 accounts open at period start. New-logo ARR is excluded. Full formula derivation in [`documentation/excel_health_scoring_model.md §4`](documentation/excel_health_scoring_model.md).

| Line item | Accounts | ARR | % of Starting ARR |
|---|---|---|---|
| **Starting ARR** | 50 | **$10,126,000** | 100.0% |
| Gross Churn ARR | 4 | ($856,000) | (8.5%) |
| Contraction ARR | 8 | ($307,650) | (3.0%) |
| Expansion ARR | 22 | $870,480 | +8.6% |
| **Ending ARR** | 46 | **$9,832,830** | 97.1% |

| Retention metric | Result | Benchmark read |
|---|---|---|
| **Net Revenue Retention (NRR)** | **97.1%** | Below the 110%+ expected of healthy B2B SaaS; the book is not self-growing |
| **Gross Revenue Retention (GRR)** | **88.5%** | The real signal — 11.5% of revenue leaks per cycle |
| **Logo churn rate** | **8.0%** | 4 of 50 accounts lost |
| NRR–GRR spread | 8.6 pts | Expansion is masking a retention problem |

**Read this as a CS leader would:** the 8.6-point gap between GRR and NRR is the whole story. Expansion is doing the work that retention should be doing. Close the GRR gap and the same expansion motion takes this book past 105%.

---

## Strategic recommendations for the Customer Success team

### 1. Make executive sponsorship a contractual onboarding deliverable — not a CSM aspiration

The 21.5-point adoption gap between sponsored and unsponsored accounts is the single largest lever in the dataset. Ten accounts ($2.1M ARR) currently have no covered decision-maker, and every modelled churn event occurred inside that group.

**Action:** require a named executive sponsor and a named internal adoption owner as a go-live exit criterion. Institute a mandatory 10-day sponsor-change protocol — when a champion departs, a re-sponsorship motion fires automatically rather than surfacing at renewal.

### 2. Adopt a 70% adoption floor and run the intervention playbook against the $4.38M below it

22 accounts sit under the floor. Nine of them are Yellow — recoverable with a structured 45-day motion before they decay into Red, where recovery economics collapse.

**Action:** trigger [`PB-ADOPT-02`](playbooks/adoption_risk_intervention.md) on every Yellow account below 70% adoption, sequenced by ARR. Target ≥55% Green conversion. Starting point: ID-1001 ($400K), ID-1002 ($394K), ID-1011 ($125K).

### 3. Train the ASM layer, not just the reps

The territory-level pattern in the data is unambiguous: adoption tracks manager inspection, not rep training volume. Where the Area Sales Manager reviews the dashboard weekly, adoption holds; where they do not, it decays back within 30 days of any training event.

**Action:** shift enablement spend from rep-volume sessions to ASM dashboard accountability. Make the weekly territory scorecard a standing artefact the CSM sends to the distributor's internal owner. Measure ASM dashboard logins as a leading health input in the next model revision.

### 4. Separate the at-risk motion from the commercial motion

Eight surviving Red accounts are modelled at 15% contraction — $307,650 of preventable leakage. Contraction is usually a licence right-sizing conversation that arrives before anyone has tried to activate the dormant seats.

**Action:** institute a rule that no downsell discussion opens on a Red or Yellow account until the adoption playbook has run its full 45 days. Align CSM and AE on renewal timing 120 days out, not 30.

### 5. Reweight the health score toward trajectory, and de-risk support noise

The current model scores a point-in-time level. In practice a Green account falling 12 points is a higher priority than a stable Yellow one, and the model cannot see that. Ticket volume is also unweighted — 20 password resets score identically to 20 sync failures.

**Action:** add a 30/60/90-day score-delta component and severity-weight the support input. Re-baseline tier thresholds after one full quarter of trend data.

### 6. Build a revenue-weighted coverage model

52.2% of ARR sits in Yellow and Red. A flat cadence across 50 accounts spreads CSM capacity evenly across unequal risk.

**Action:** tier CSM cadence by ARR-at-risk rather than by account count — weekly touch on the top five at-risk accounts ($2.07M), monthly on the remaining at-risk book, quarterly on Green with a scheduled expansion review.

---

## Methodology note

Health scores weight field rep adoption at 40%, order volume trend at 25%, support burden at 20% and executive sponsorship at 15% — deliberately adoption-heavy, because in field sales automation a licence that never opens on the beat generates no value to renew against. Churn, contraction and expansion rates are modelled assumptions applied uniformly to tiered cohorts, not observed renewal outcomes; in production these inputs would be replaced with actual renewal-event data. Full derivation, limitations and Excel formulas are documented in [`documentation/excel_health_scoring_model.md`](documentation/excel_health_scoring_model.md).
