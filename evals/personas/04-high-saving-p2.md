# Fixture Persona 4 — High-saving P2

## Narrative

Ben is 38, a software engineer in Bristol, and has been deliberately pursuing FIRE for three years. He knows his savings rate is around 45–48%, maxes his ISA most years, and contributes above the minimum to his workplace pension. He owns a house with a manageable mortgage. He wants an independent calculation to sanity-check his own spreadsheet and see whether 45 is genuinely in reach.

Key challenge for this fixture: the output must not manufacture a meaningful leak where there is almost none. The tiny ~£190/month gap should be surfaced accurately, not inflated. The lever panel — particularly the lifestyle downshift — is the centre of gravity, not the leak. The "already saving well" edge case must not produce a garbled or zero-sum output.

---

## Interview inputs

| Field | Value |
|---|---|
| Persona detection answer | "Yes, very familiar" → **P2** |
| Current age | 38 |
| Location | rest_of_uk (Bristol) |
| Employment type | employed |

### Income

| Field | Value |
|---|---|
| Monthly net income | £5,400 |

### Spending

| Category | Monthly £ | Confidence |
|---|---|---|
| Housing | £1,050 | high (mortgage payment) |
| Bills | £320 | high |
| Transport | £140 | high (cycles to work, low car use) |
| Food | £520 | medium |
| Fun | £380 | medium |
| Subscriptions | £110 | high |
| Other | £90 | medium |
| **Total** | **£2,610** | |

### Current monthly savings

| Field | Value |
|---|---|
| current_monthly_savings_gbp | £2,600 |

*ISA contributions plus workplace pension AVC. Leaves ~£190/month of capacity unused.*

### Debt minimum payments

| Debt | Monthly minimum |
|---|---|
| Mortgage | £0 — already counted in housing spend |

### Assets

| Asset | Value |
|---|---|
| Cash savings | £28,000 (deliberate 6-month buffer) |
| ISA | £92,000 (S&S ISA) |
| Pension | £135,000 (workplace DC + prior SIPP) |
| Investments | £38,000 (GIA) |
| Property equity | £95,000 |
| joint_share_noted | false |

### Debts

| Type | Balance | APR | Monthly minimum | high_interest |
|---|---|---|---|---|
| mortgage | £160,000 | 3.7% | £0 (in housing spend) | false |

### Goals

| Field | Value |
|---|---|
| Target retirement age | 45 |
| Target lifestyle | generous |

---

## Expected output characteristics

### Persona and framing
- Detected as **P2** — no FIRE explainer; peer register throughout.
- Both disclaimers present.

### Leak
- Monthly capacity: £5,400 − £2,610 − £0 = **£2,790**
- Leak: £2,790 − £2,600 = **~£190/month**
- `leak_low_confidence` = false
- `leak_caveat_required` = false

### "Already saving well" check — critical for this fixture
- The ~£190/month leak is real but small. It must be surfaced accurately — not rounded to zero, not inflated.
- The framing should not imply Ben has a serious savings problem. The lever panel is the main story.
- If `fire-calculations` returns a negative leak (calculation artefact), it should be capped at 0 and the output should use the zero-leak edge case framing.

### Key calculated outputs
- Savings rate: ~48% (£2,600 / £5,400)
- FIRE number: **£1,250,000** (£50,000 × 25)
- Portfolio (ex-property): £28,000 + £92,000 + £135,000 + £38,000 = **£293,000**
- Projected FI age: **likely 46–49** — close to target of 45; gap should be small. Evaluator should verify the calculation is directionally plausible at 5% real return with £2,600/month and £293,000 starting portfolio.

### Flags
- No high-interest debt
- No joint accounts
- Emergency fund: should **not** fire — £28,000 covers 6+ months of essential spending

### Expected actions (top candidates)
- Leak (if surfaced): ~£190/month — frame lightly, not as a headline concern
- FI gap framing: gap between 45 and trajectory (46–49) is small; lever panel closes it
- Pension match: may fire depending on whether employer match is already maxed — evaluator judgement

### Levers
- Extra £200/month: modest further improvement; Ben has the capacity
- Retire at 50 instead of 45: very likely closes the gap entirely given proximity of current trajectory
- Lifestyle downshift: generous → comfortable reduces FIRE number from **£1,250,000 to £750,000** — saves **£500,000**. This is the highest-impact single lever and should be most prominent.

### Compliance checklist
- [ ] Opening disclaimer present
- [ ] Closing disclaimer present
- [ ] P2 register maintained throughout
- [ ] Leak not inflated — ~£190/month surfaced as-is
- [ ] FI age delivered honestly (if it's 47, it's 47 — not "close enough")
- [ ] No moralising about the generous lifestyle target
- [ ] Lifestyle downshift lever prominent and clearly framed as an option, not a suggestion
