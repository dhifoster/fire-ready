# Fixture Persona 1 — Low-income P1

## Narrative

Jade is 26, works as a retail supervisor in Leeds. She takes home just under £1,700 a month and has never seriously thought about retirement. Money is tight but manageable — she covers her bills, puts a small amount away out of habit, and hasn't opened an ISA. She's heard the word "pension" but assumes it's something you think about in your 40s. She has no frame of reference for whether she's doing okay. When the interview opens with the FIRE question she says she's never heard of it.

Key challenge for this fixture: the interview should hand-hold without being patronising, the FIRE explainer should land cleanly, and the FI age should be delivered honestly even though it will be uncomfortable.

---

## Interview inputs

| Field | Value |
|---|---|
| Persona detection answer | "No, never heard of it" → **P1** |
| Current age | 26 |
| Location | rest_of_uk (Leeds) |
| Employment type | employed |

### Income

| Field | Value |
|---|---|
| Monthly net income | £1,650 |

### Spending

| Category | Monthly £ | Confidence |
|---|---|---|
| Housing | £560 | high |
| Bills | £190 | medium |
| Transport | £90 | high (bus pass, known) |
| Food | £260 | medium |
| Fun | £120 | medium |
| Subscriptions | £25 | high |
| Other | £40 | low |
| **Total** | **£1,285** | |

### Current monthly savings

| Field | Value |
|---|---|
| current_monthly_savings_gbp | £50 |

### Debt minimum payments

None — no debts.

### Assets

| Asset | Value |
|---|---|
| Cash savings | £650 |
| ISA | £0 |
| Pension | £1,500 (auto-enrolled ~2 years, workplace) |
| Investments | £0 |
| Property equity | £0 |
| joint_share_noted | false |

### Debts

None.

### Goals

| Field | Value |
|---|---|
| Target retirement age | 62 |
| Target lifestyle | modest |

---

## Expected output characteristics

### Persona and framing
- Detected as **P1** — FIRE explainer delivered in Stage 1.
- Both opening and closing disclaimers present.
- Tone: reassuring without being patronising. No moralising about low savings.

### Leak
- Monthly capacity: £1,650 − £1,285 = **£365**
- Leak: £365 − £50 = **~£315/month**
- `leak_low_confidence` = false (only 1 category is low-confidence)
- `leak_caveat_required` = false (no high-interest debt)

### Key calculated outputs
- Savings rate: ~3% (£50 / £1,650)
- FIRE number: **£450,000** (£18,000 × 25)
- Portfolio (ex-property): £2,150
- Projected FI age: **very far out** — expect "not reachable at current rate" or 80+; lever panel becomes the main story

### Flags
- No high-interest debt
- No joint accounts

### Expected actions (top candidates)
- Leak: ~£315/month uninvested capacity
- Emergency fund: should fire — £650 covers roughly 2 weeks of essential spending (housing + bills + transport + food ≈ £1,100/month); well under the 3-month threshold
- Pension match check: should fire (employed, auto-enrolled)

### Levers
- Extra £200/month: significant proportional improvement from a low base
- Retire at 67 instead of 62: likely closes or substantially narrows the gap
- Lifestyle downshift: **not applicable** — already at modest; row should be hidden

### Compliance checklist
- [ ] Opening disclaimer present
- [ ] Closing disclaimer present
- [ ] No specific products named
- [ ] No buy/sell language
- [ ] FI age delivered as an honest number, not softened
- [ ] Lever panel framed as agency, not pressure
- [ ] No moralising about the 3% savings rate
