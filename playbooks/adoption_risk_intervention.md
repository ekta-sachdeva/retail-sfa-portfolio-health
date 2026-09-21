# Playbook: Adoption Risk Intervention (Yellow Tier)

**Playbook ID:** PB-ADOPT-02
**Applies to:** Yellow (At-Risk) accounts with low field rep adoption
**Owner:** Assigned CSM
**Duration:** 45 days from trigger to outcome disposition
**Escalation path:** CSM → CS Manager (Day 30) → VP Customer Success (Day 45 if unresolved)

---

## 1. Trigger condition

The playbook fires automatically when an account meets the tier condition **and** any one adoption condition.

**Tier condition (required):** Health Score between 52 and 71.9 (Yellow).

**Adoption conditions (any one):**

| # | Condition | Threshold |
|---|---|---|
| T1 | 30-day active rep usage below the adoption floor | `Active_Reps_Last_30_Days_Pct < 70%` |
| T2 | Hard escalation — critical dormancy | `Active_Reps_Last_30_Days_Pct < 60%` → skip to Step 2 within 48 hrs |
| T3 | Adoption cliff | Usage drops ≥ 15 percentage points month-over-month |
| T4 | Silent decay | Order volume trend negative for 3 consecutive weeks while licences unchanged |

**Suppression rules — do not fire if:**
- Account is inside its first 60 days of onboarding (route to `PB-ONBOARD-01` instead).
- An open P1 support incident exists — adoption is blocked by a product fault, not a behaviour gap. Resolve the incident first.
- The playbook has completed within the last 90 days without a tier change (route to Red escalation instead of re-running).

**Current portfolio match:** 9 of 16 Yellow accounts, representing $1.28M ARR. Highest-value match: **BR-1001 Shree Balaji Distributors** — $400K ARR, 322 licensed reps, 62% adoption, −8.3% order trend, Passive sponsor.

---

## 2. Pre-work — Diagnose before you call (Days 1–3)

Never open the intervention without a hypothesis. Complete this before any customer contact.

| Task | Output |
|---|---|
| Pull rep-level usage export; split licensed reps into **Never Activated**, **Lapsed** (active >30 days ago), **Active** | Named list per bucket |
| Segment dormancy by geography, ASM territory and device type | Is this one region or the whole book? |
| Review last 90 days of support tickets for repeat themes (sync failures, GPS drain, order-form friction) | Top 3 friction themes |
| Check order value processed through the platform vs. the distributor's total secondary sales | Value leakage estimate |
| Confirm renewal date, contract value, licence count and sponsor status in CRM | Commercial context |

**Diagnostic branch — this determines the whole intervention:**

- **Never Activated majority** → onboarding/enablement failure. Weight the plan toward Step 4 (field training).
- **Lapsed majority** → the product was tried and abandoned. Weight toward Step 3 (workflow friction) — find out what broke.
- **Regionally concentrated** → a single ASM or territory manager is not enforcing usage. Weight toward Step 3 (management accountability), not training.

---

## 3. Step-by-step CSM action items

### Step 1 — Internal alignment (Day 3)

- Brief the Account Executive; confirm renewal date and any open commercial conversation so the intervention and the commercial motion do not contradict each other.
- Book 15 minutes with Support and Product to confirm whether the top friction themes have fixes shipped, scheduled, or neither. **Do not promise an unscheduled fix.**
- Log the opening state in the CRM: current adoption %, health score, ARR at risk, target adoption, target date.
- Set the success criterion now, in writing: **adoption ≥ 75% and a positive order-volume trend by Day 45.**

### Step 2 — Stakeholder alignment call (Days 5–7)

Audience: Head of Sales (economic buyer), National Sales Manager, IT/Ops lead. **45 minutes. Not a demo.**

- Open with their business metric, not ours: secondary sales coverage, outlet productivity, beat compliance.
- Present the dormancy split — *"322 licences, 200 reps active. 122 reps are working your market without visibility."*
- Quantify the gap in their terms: dormant licence cost, unmeasured outlets, orders still moving on WhatsApp and paper.
- Ask the diagnostic question and stop talking: *"What is stopping your reps from using it on the beat?"*
- Establish sponsorship explicitly. A Passive sponsor is the root cause of most Yellow accounts — ask for a named internal owner and a stated expectation from the Head of Sales to the ASM layer.
- **Close with a commitment, not a follow-up:** a named internal owner, agreed adoption target, and a date for the field training.

**Exit criteria:** written confirmation of the internal owner and the adoption target. Without both, do not proceed — escalate to the CS Manager instead.

### Step 3 — Remove the friction (Days 8–14)

- Fix what the diagnostic surfaced: reconfigure order forms, prune mandatory fields, correct beat plans and outlet master data, resolve sync or device issues.
- Trim licences that are genuinely unneeded **only after** the commercial conversation with the AE. Never surface a downsell before the retention motion has run.
- Publish a one-page "what changed" note the distributor can forward to the field so reps see a response to their complaints.

### Step 4 — Field rep enablement (Days 15–21)

- Run **role-specific, territory-based sessions** — one per ASM territory, maximum 25 reps, in the local language.
- Format: 30 minutes, on-device, using the rep's own live beat. No slideware.
- Cover the three actions that drive the metric: start beat → log order at outlet → close day. Everything else is optional.
- Train the ASM layer separately on the dashboard. **Rep adoption follows manager inspection, not rep enthusiasm** — if the ASM never opens the dashboard, adoption decays back within a month.
- Leave behind: a one-page vernacular quick-reference and a named WhatsApp support channel for the first two weeks.
- Track attendance against the Never Activated list by name and share the gap list with the internal owner.

### Step 5 — Week 2 pulse (Day 21)

- Check adoption delta. Expect a visible lift within 7 days of training; if adoption has not moved at all, the problem is managerial accountability, not capability — return to the sponsor immediately.
- Send the internal owner a short weekly scorecard: active reps, orders logged, territory leaderboard. Make the metric visible.

### Step 6 — 30-Day check-in (Day 30)

Formal review with the Head of Sales and the internal owner.

- Present before/after: adoption %, orders logged through the platform, outlet coverage, health score movement.
- Recognise the top-performing territory by name — peer comparison is the most effective lever in distributor field organisations.
- Agree the plan for remaining dormant territories.
- **Disposition the account:**

| Observed state at Day 30 | Disposition |
|---|---|
| Adoption ≥ 75%, trend positive | Recovering. Continue to Day 45 confirmation, then return to standard cadence. |
| Adoption 65–75%, improving | Extend 30 days. Re-run Steps 4–5 for lagging territories only. |
| Adoption flat or declining, sponsor engaged | Escalate to CS Manager. Joint executive session required. |
| Sponsor unresponsive across the cycle | Downgrade to Red. Route to `PB-EXEC-ESC-01`. Notify the AE — renewal is at risk. |

### Step 7 — Day 45 closure

- Confirm sustained adoption (a lift that holds for 30 days is real; a lift that holds for 7 days is a training-day artefact).
- Update health score and tier; record the intervention outcome and root cause in the CRM for pattern analysis.
- If recovered and adoption is above 80%, flag to the AE as **expansion-eligible** — a recovered account with a newly engaged sponsor is the strongest upsell candidate in the book.

---

## 4. Intervention email template

Sent at Step 2, addressed to the Distributor's Head of Sales. Replace every bracketed field; do not send without the diagnostic from §2 complete.

> **Subject:** [Distributor Name] — 122 of your 322 field reps are working without visibility
>
> Hi [First Name],
>
> I've been reviewing how the platform is being used across your territories ahead of our quarterly review, and I want to flag something before it affects your secondary sales numbers.
>
> Of the [322] rep licences active on your account, [200] reps logged activity in the last 30 days. That leaves **[122] reps servicing outlets without their orders, coverage or beat compliance being captured** — and your weekly order volume through the platform is down [8.3%] over the same period.
>
> The gap is concentrated in [Region / ASM territory], where adoption is at [XX%] against [YY%] in your strongest territory, [Region]. That tells me this is fixable and specific, not a broad rollout problem.
>
> I'd like 45 minutes with you and [National Sales Manager] this week to walk through three things:
>
> 1. The territory-level adoption picture, so you can see exactly where visibility is being lost.
> 2. What your reps are telling us is getting in their way — we've already identified [top friction theme] and can resolve it on our side.
> 3. A 30-day plan to bring the dormant territories to parity, including on-ground training sessions run per territory in [local language], at no additional cost to you.
>
> One thing I'd ask in advance: the territories that recover fastest are consistently the ones where the ASM reviews the dashboard weekly. If you can nominate an internal owner on your side to hold that cadence, we'll build the plan around them.
>
> Would [Day, Time] or [Day, Time] work? Happy to work around your beat cycle.
>
> Best regards,
> [CSM Name]
> Customer Success Manager, [Vendor Name]
> [Phone] · [Email]

**Drafting notes:**
- Lead with their number, not our feature. The subject line is a business exposure, not a check-in.
- Name a specific territory. Generic "usage is low" emails get generic replies.
- Offer the fix and the ask in the same message — the internal-owner request is the single most important line in the email.
- Keep the friction theme concrete but never commit to an unscheduled product fix.
- If there is no reply in 72 hours, call. Do not send a second email.

---

## 5. Success metrics

| Metric | Target | Measured |
|---|---|---|
| Active rep adoption | ≥ 75% | Day 45 |
| Weekly order volume trend | Positive | Day 45 |
| Health score | ≥ 72 (Green) | Day 45 |
| Executive sponsor status | Engaged | Day 30 |
| Playbook-to-Green conversion rate | ≥ 55% of triggered accounts | Quarterly |
| ARR retained from triggered cohort | ≥ 90% | At renewal |
