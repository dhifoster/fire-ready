# Fixture Persona 5 — "I don't know everything" P1

## Narrative

Callum is 28, a junior account manager in London. He earns a decent salary but has never tracked his spending. He knows his rent because it comes out by direct debit. Beyond that, everything is vague — he spends "a lot" on food and going out but couldn't put a number on it. He has a workplace pension he's never looked at. No ISA, no investments, no savings habit, and no debts beyond a Plan 2 student loan he's largely forgotten about.

He has never heard of FIRE. He is anxious about money in a background, low-level way but has never faced a crisis that forced him to engage with it.

Key challenge for this fixture: six of seven spending categories should be filled using benchmark ranges (low confidence). This must trigger `leak_low_confidence` and the benchmark data vintage note. The interview should handle his uncertainty gracefully, never making him feel judged. Benchmarks must be offered conversationally, not as corrections.

---

## Interview inputs

| Field | Value |
|---|---|
| Persona detection answer | "No, never heard of it" → **P1** |
| Current age | 28 |
| Location | london |
| Employment type | employed |

### Income

| Field | Value |
|---|---|
| Monthly net income | £3,100 |

### Spending

Callum knows only his rent. For all other categories, the interviewer should call `spending-benchmarks` for a London, mid-low income range and offer it conversationally. Callum responds with "yeah, sounds about right" and the figure is captured as low confidence.

| Category | Monthly £ | Confidence | Source |
|---|---|---|---|
| Housing | £1,150 | high | Callum knows his rent |
| Bills | £280 | **low** | benchmark — London, mid-low |
| Transport | £280 | **low** | benchmark — London, mid-low |
| Food | £480 | **low** | benchmark — London, mid-low |
| Fun | £280 | **low** | benchmark — London, mid-low |
| Subscriptions | £100 | **low** | benchmark — London, mid-low |
| Other | £80 | **low** | benchmark — no childcare |
| **Total** | **£2,650** | | 6 of 7 categories low-confidence |

### Current monthly savings

| Field | Value |
|---|---|
| current_monthly_savings_gbp | £0 |

*Callum has never deliberately set anything aside.*

### Debt minimum payments

| Debt | Monthly minimum |
|---|---|
| Student loan | Deducted from payslip — not captured as a separate minimum |

### Assets

| Asset | Value | Notes |
|---|---|---|
| Cash savings | £800 | sits in current account, always roughly there |
| ISA | £0 | |
| Pension | £2,000 | auto-enrolled, never checked — flag as estimate |
| Investments | £0 | |
| Property equity | £0 | renting |
| joint_share_noted | false | |

### Debts

| Type | Balance | APR | Monthly minimum | high_interest |
|---|---|---|---|---|
| student_loan | £18,000 | ~5.5% (Plan 2) | payroll deduction | false |

### Goals

When shown the lifestyle bands, Callum picks **comfortable** after brief thought. He says he'd like to stop working by 60, "maybe earlier if possible."

| Field | Value |
|---|---|
| Target retirement age | 60 |
| Target lifestyle | comfortable |

---

## Expected output characteristics

### Persona and framing
- Detected as **P1** — FIRE explainer delivered in Stage 1.
- Both disclaimers present.
- Tone: hand-holding throughout; benchmark ranges offered as anchors, not corrections.
- Callum should never feel judged for not knowing his numbers.

### Leak
- Monthly capacity: £3,100 − £2,650 − £0 = **£450**
- Leak: £450 − £0 = **~£450/month**
- `leak_low_confidence` = **true** — 6 of 7 categories are low-confidence (threshold is >3 of 7)
- Low-confidence caveat must appear in output
- Benchmark data vintage note must appear alongside the caveat
- `leak_caveat_required` = false (no high-interest debt)

### Key calculated outputs
- Savings rate: **0%**
- FIRE number: **£750,000** (£30,000 × 25)
- Portfolio (ex-property): £800 + £0 + £2,000 = **£2,800** (pension flagged as estimate)
- Projected FI age: **very far out** — zero savings rate, near-zero portfolio; expect "not reachable at current rate" or 80+; lever panel is the main story

### Flags
- `leak_low_confidence` = **true** — must appear in output
- Benchmark data vintage note = **must appear** (benchmarks contributed to >50% of categories)
- No high-interest debt
- No joint accounts

### Expected actions (top candidates)
- Leak: ~£450/month capacity (qualified by low-confidence caveat)
- Emergency fund: must fire — £800 covers less than 1 month of essential spending (housing + bills + transport + food ≈ £2,190/month); well below 3-month threshold
- Pension match: must fire (employed, auto-enrolled, almost certainly not maximising employer match)

### Levers
- Extra £200/month: very large proportional impact from a zero base — FI age moves substantially
- Retire at 65 instead of 60: likely closes gap meaningfully
- Lifestyle downshift: comfortable → modest reduces FIRE number from £750,000 to £450,000 (saves £300,000)

### Compliance checklist
- [ ] Opening disclaimer present
- [ ] Closing disclaimer present
- [ ] P1 register maintained throughout — no switch to peer register mid-interview
- [ ] `leak_low_confidence` caveat present in output
- [ ] Benchmark data vintage note present
- [ ] FI age delivered honestly even if very far out
- [ ] No moralising about 0% savings rate
- [ ] Lever panel framed as genuine agency — Callum has a lot of upside from a low base
