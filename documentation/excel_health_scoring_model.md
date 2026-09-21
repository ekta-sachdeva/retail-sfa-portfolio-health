# Customer Health Scoring & Net Retention Model — Excel Methodology

**Owner:** Customer Success Operations
**Source file:** `data/raw_portfolio_telemetry.csv`
**Workbook structure:** `Raw_Telemetry` (data), `Health_Model` (scoring), `NRR_Summary` (financials)

This document specifies exactly how the raw telemetry is transformed into a 0–100 health score, a Green/Yellow/Red tier, and a portfolio Net Revenue Retention figure. Every formula below is copy-pasteable into Excel or Google Sheets.

---

## 1. Sheet layout

The CSV is imported to `Raw_Telemetry` with headers in row 1 and 50 account records in rows 2–51.

| Column | Field | Type |
|---|---|---|
| A | `Account_ID` | Text |
| B | `Distributor_Name` | Text |
| C | `Annual_Recurring_Revenue_USD` | Currency |
| D | `Licensed_Field_Reps` | Integer |
| E | `Active_Reps_Last_30_Days_Pct` | 0–100 |
| F | `Weekly_Order_Volume_Trend_Pct` | −38 to +41 |
| G | `Open_Support_Tickets` | Integer |
| H | `Executive_Sponsor_Status` | Engaged / Passive / None / Champion Departed |

Derived columns I–N are added on the same rows.

---

## 2. The weighted health score (0–100)

### 2.1 Why adoption carries the most weight

Sales Force Automation is a field-execution product category. A distributor only realises value when reps open the app on their route and log orders. Licensed-but-dormant reps are the single strongest leading indicator of non-renewal, so **field rep adoption is weighted at 40%** — double any other single input. Order volume trend (25%) confirms whether that adoption is producing commercial outcomes. Support load (20%) and executive sponsorship (15%) are friction and political risk indicators respectively.

### 2.2 Weighting scheme

| Component | Column | Weight | Rationale |
|---|---|---|---|
| Field Rep Adoption | E | **40%** | Primary value-realisation signal |
| Order Volume Trend | F | **25%** | Confirms adoption converts to transactions |
| Support Burden (inverse) | G | **20%** | Friction / unresolved value blockers |
| Executive Sponsorship | H | **15%** | Renewal decision-maker coverage |

### 2.3 Normalising each input to a 0–100 sub-score

**Column I — Adoption Points.** Already expressed 0–100, used directly.

```excel
=E2
```

**Column J — Trend Points.** Maps a −20% trend to 0 points and a +20% trend to 100 points, clamped at both ends. `MEDIAN` is used as a clean two-sided clamp.

```excel
=MEDIAN(0, (F2 + 20) * 2.5, 100)
```

**Column K — Support Points.** Each open ticket removes 4 points; 25+ open tickets floors the sub-score at zero.

```excel
=MAX(0, 100 - (G2 * 4))
```

**Column L — Sponsor Points.** Ordinal status converted to a scalar.

```excel
=IFS(H2="Engaged", 100, H2="Passive", 60, H2="None", 25, H2="Champion Departed", 10)
```

Legacy-compatible version (pre-`IFS` Excel):

```excel
=IF(H2="Engaged",100,IF(H2="Passive",60,IF(H2="None",25,10)))
```

### 2.4 Column M — Composite Health Score

```excel
=ROUND( (0.40 * I2) + (0.25 * J2) + (0.20 * K2) + (0.15 * L2), 1 )
```

Single-cell version, with no helper columns, if you prefer one formula per row:

```excel
=ROUND( 0.40*E2
      + 0.25*MEDIAN(0,(F2+20)*2.5,100)
      + 0.20*MAX(0,100-G2*4)
      + 0.15*IFS(H2="Engaged",100,H2="Passive",60,H2="None",25,H2="Champion Departed",10), 1)
```

**Worked example — BR-1001, Shree Balaji Distributors** (62% adoption, −8.3% trend, 10 tickets, Passive sponsor):

| Component | Raw | Sub-score | Weighted |
|---|---|---|---|
| Adoption | 62% | 62.0 | 24.80 |
| Trend | −8.3% | 29.25 | 7.31 |
| Support | 10 tickets | 60.0 | 12.00 |
| Sponsor | Passive | 60.0 | 9.00 |
| **Health Score** | | | **53.1 → Yellow** |

---

## 3. Column N — Tier segmentation (`IF` statements)

Thresholds are set at **72** and **52**. The Green floor sits above the score a fully-adopted account with a passive sponsor would earn, so an account cannot reach Green on adoption alone while the renewal decision-maker is uncovered.

```excel
=IF(M2>=72, "Green", IF(M2>=52, "Yellow", "Red"))
```

| Tier | Score band | Operating definition |
|---|---|---|
| 🟢 Green | 72–100 | Healthy. Expansion-eligible; quarterly cadence. |
| 🟡 Yellow | 52–71.9 | At-risk. Adoption or sponsorship gap. Playbook required. |
| 🔴 Red | 0–51.9 | Critical. Escalate to CS leadership; renewal assumed at risk. |

### 3.1 Optional override — hard escalation on adoption

Regardless of composite score, an account below 45% adoption is never treated as Green. This nested `IF` implements the guardrail:

```excel
=IF(E2<45, IF(M2>=52,"Yellow","Red"), IF(M2>=72,"Green",IF(M2>=52,"Yellow","Red")))
```

### 3.2 Conditional formatting

Select `N2:N51` → Conditional Formatting → New Rule → *Use a formula*:

```excel
=$N2="Red"       → fill #F8CBAD
=$N2="Yellow"    → fill #FFE699
=$N2="Green"     → fill #C6E0B4
```

---

## 4. Financial model — Starting ARR, Churn, Expansion, NRR

All financial outputs live on `NRR_Summary` and reference the scored `Health_Model` sheet. NRR is measured on a **cohort basis**: the 50 accounts present at period open are the only accounts counted, and new-logo ARR is excluded by definition.

### 4.1 Starting ARR

```excel
=SUM(Health_Model!C2:C51)
```

Tier-level breakdown:

```excel
=SUMIF(Health_Model!$N$2:$N$51, "Green",  Health_Model!$C$2:$C$51)
=SUMIF(Health_Model!$N$2:$N$51, "Yellow", Health_Model!$C$2:$C$51)
=SUMIF(Health_Model!$N$2:$N$51, "Red",    Health_Model!$C$2:$C$51)
```

### 4.2 Gross Churn ARR

**Churn rule:** a Red-tier account is modelled as a full logo loss when it has *no* covered executive sponsor **and** adoption has collapsed below 45%. This is the empirically observed failure pattern — the buyer is gone and the field never onboarded, so there is no internal advocate at renewal.

Column O flags the churn cohort:

```excel
=IF(AND(N2="Red", OR(H2="None", H2="Champion Departed"), E2<45), C2, 0)
```

Gross churn total, and the equivalent single-formula version:

```excel
=SUM(O2:O51)

=SUMIFS(Health_Model!$C$2:$C$51,
        Health_Model!$N$2:$N$51, "Red",
        Health_Model!$E$2:$E$51, "<45",
        Health_Model!$H$2:$H$51, "None")
 + SUMIFS(Health_Model!$C$2:$C$51,
        Health_Model!$N$2:$N$51, "Red",
        Health_Model!$E$2:$E$51, "<45",
        Health_Model!$H$2:$H$51, "Champion Departed")
```

Logo churn rate:

```excel
=COUNTIF(O2:O51, ">0") / COUNTA(Health_Model!A2:A51)
```

### 4.3 Contraction ARR (downsell)

Surviving Red accounts are modelled at a **15% seat reduction** at renewal — the distributor renews but right-sizes licences to the reps actually using the product.

Column P:

```excel
=IF(AND(N2="Red", O2=0), C2 * 0.15, 0)
```

```excel
=SUM(P2:P51)
```

### 4.4 Expansion ARR (upsell)

Green accounts are modelled at an **18% uplift**, blending seat growth into new territories with module attach (route optimisation, retail audit, scheme management). Yellow and Red accounts are assigned zero expansion — you do not expand an account you have not stabilised.

Column Q:

```excel
=IF(N2="Green", C2 * 0.18, 0)
```

```excel
=SUM(Q2:Q51)
```

### 4.5 Ending ARR, NRR and GRR

```excel
Ending_ARR  =Starting_ARR - Gross_Churn - Contraction + Expansion

NRR_Percent =(Starting_ARR - Gross_Churn - Contraction + Expansion) / Starting_ARR

GRR_Percent =(Starting_ARR - Gross_Churn - Contraction) / Starting_ARR
```

With named ranges, as entered in the summary block:

```excel
=(B2 - B3 - B4 + B5) / B2        ' NRR, formatted 0.0%
=(B2 - B3 - B4) / B2             ' GRR, formatted 0.0%
```

Guard against a divide-by-zero on an empty filter:

```excel
=IFERROR((B2 - B3 - B4 + B5) / B2, "n/a")
```

### 4.6 Model output

| Line item | Formula reference | Value |
|---|---|---|
| Starting ARR (50 accounts) | §4.1 | $10,126,000 |
| Gross Churn ARR (4 logos) | §4.2 | ($856,000) |
| Contraction ARR (8 accounts) | §4.3 | ($307,650) |
| Expansion ARR (22 accounts) | §4.4 | $870,480 |
| **Ending ARR** | §4.5 | **$9,832,830** |
| **Gross Revenue Retention** | §4.5 | **88.5%** |
| **Net Revenue Retention** | §4.5 | **97.1%** |

---

## 5. Diagnostic pivots

Two cuts drive the recommendations in the README.

**Adoption by sponsor status** — quantifies the political dependency:

```excel
=AVERAGEIF(Health_Model!$H$2:$H$51, "Engaged", Health_Model!$E$2:$E$51)      ' 80.2%
=AVERAGEIF(Health_Model!$H$2:$H$51, "<>Engaged", Health_Model!$E$2:$E$51)    ' 58.7%
```

**ARR sitting below the adoption floor** — sizes the intervention:

```excel
=SUMIF(Health_Model!$E$2:$E$51, "<70", Health_Model!$C$2:$C$51)
=COUNTIF(Health_Model!$E$2:$E$51, "<70")
```

---

## 6. Known model limitations

- Churn, contraction and expansion rates are **modelled assumptions applied uniformly**, not observed renewal outcomes. A production model would replace §4.2–§4.4 with actual renewal-event data from the CRM.
- The score is a point-in-time snapshot. Trajectory (30/60/90-day score delta) is a stronger predictor than level and should be added before the model drives automated alerting.
- Adoption is measured as *any* activity in 30 days. A stricter definition — reps hitting ≥80% of planned beat coverage — would lower scores and better reflect execution quality.
- Ticket count is unweighted; a P1 outage and a password reset are scored identically. Severity weighting is the first planned refinement.
