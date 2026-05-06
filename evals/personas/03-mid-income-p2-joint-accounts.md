# Fixture Persona 3 — Mid-income P2 with joint accounts

## Narrative

Priya is 34, a product manager in London earning a good salary. She and her partner bought a flat together two years ago. She's been reading about FIRE for the past year, has a rough sense of her savings rate, and wants a sharper number. The joint accounts complicate things — she's unsure whether to count the full mortgage or just her half, and the same for equity. She's been contributing to her workplace pension and topping up her ISA most years, but not every year.

Key challenge for this fixture: the interview must consistently capture Priya's personal share of all joint assets and liabilities throughout, set `joint_share_noted = true`, and surface this clearly in the read-back. The FI gap (target 47, trajectory likely mid-50s) should be delivered honestly.

---

## Interview inputs

| Field | Value |
|---|---|
| Persona detection answer | "Yes, definitely" → **P2** |
| Current age | 34 |
| Location | london |
| Employment type | employed |

### Income

| Field | Value |
|---|---|
| Monthly net income | £4,100 |

### Spending

All figures are Priya's personal share of joint costs plus her own individual spend.

| Category | Monthly £ | Confidence |
|---|---|---|
| Housing | £875 | high (her half of £1,750 joint mortgage payment) |
| Bills | £310 | medium (her share of joint bills) |
| Transport | £195 | high (Oyster + occasional taxi) |
| Food | £480 | medium |
| Fun | £320 | medium |
| Subscriptions | £90 | high |
| Other | £85 | medium |
| **Total** | **£2,355** | |

### Current monthly savings

| Field | Value |
|---|---|
| current_monthly_savings_gbp | £900 |

### Debt minimum payments

| Debt | Monthly minimum |
|---|---|
| Mortgage (personal share) | £0 — already counted in housing spend |

### Assets

All figures are Priya's estimated personal share.

| Asset | Value | Notes |
|---|---|---|
| Cash savings | £9,500 | personal account |
| ISA | £28,000 | S&S ISA |
| Pension | £24,000 | workplace DC |
| Investments | £0 | |
| Property equity | £45,000 | her estimated half of ~£90,000 total equity |
| **joint_share_noted** | **true** | flagged when property/mortgage raised |

### Debts

| Type | Balance (personal share) | APR | Monthly minimum | high_interest |
|---|---|---|---|---|
| mortgage | £130,000 | 4.2% | £0 (in housing spend) | false |

### Goals

| Field | Value |
|---|---|
| Target retirement age | 47 |
| Target lifestyle | comfortable |

---

## Expected output characteristics

### Persona and framing
- Detected as **P2** — FIRE explainer skipped entirely; peer register throughout.
- Both disclaimers still present.
- No hand-holding; move directly to Stage 2 after persona detection.

### Leak
- Monthly capacity: £4,100 − £2,355 − £0 (mortgage min already in housing) = **£1,745**
- Leak: £1,745 − £900 = **~£845/month**
- `leak_low_confidence` = false
- `leak_caveat_required` = false (mortgage APR 4.2% is below 10% threshold)

### Key calculated outputs
- Savings rate: ~22% (£900 / £4,100)
- FIRE number: **£750,000** (£30,000 × 25)
- Portfolio (ex-property): £9,500 + £28,000 + £24,000 = **£61,500**
- Projected FI age: **likely mid-to-late 50s** — clear gap from target of 47; lever panel should be prominent

### Flags
- `joint_share_noted` = **true** — snapshot must note that all figures represent Priya's personal share, not joint totals
- No high-interest debt

### Expected actions (top candidates)
- Leak: ~£845/month uninvested capacity
- Pension match check: should fire (employed)
- FI gap framing: gap between target (47) and trajectory (mid-50s) is material

### Levers
- Extra £200/month: meaningful but modest improvement on an already solid savings rate
- Retire at 52 instead of 47: likely closes the gap entirely or comes very close
- Lifestyle downshift: comfortable → modest reduces FIRE number from £750,000 to £450,000 (saves £300,000)

### Compliance checklist
- [ ] Opening disclaimer present
- [ ] Closing disclaimer present
- [ ] P2 register maintained throughout — no FIRE explainer
- [ ] Joint share noted in read-back and snapshot
- [ ] FI age delivered soberly even if 15+ years past her target
- [ ] No moralising
