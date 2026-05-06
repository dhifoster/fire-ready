# Fixture Persona 2 — Mid-income P1 with high-interest debt

## Narrative

Marcus is 31, a marketing manager in Birmingham earning a decent salary but feeling financially stuck. A credit card balance crept up during a difficult period and he's been paying the minimum ever since. He spends freely on weekends and doesn't track closely. He's vaguely heard of FIRE — maybe on Reddit — but assumed it was for people earning far more than him. He says "sort of, I've seen it online" when asked, which the interview should treat as vague and give the P1 explainer. He's saving almost nothing beyond his workplace pension minimum; the credit card interest is quietly eating his headroom.

Key challenge for this fixture: the high-interest debt flag and leak caveat must both fire; the debt must be surfaced neutrally with no prescription; the large gap between target (55) and trajectory (~70s) must be delivered honestly.

---

## Interview inputs

| Field | Value |
|---|---|
| Persona detection answer | "Sort of, I've seen it online" — vague → **P1** |
| Current age | 31 |
| Location | rest_of_uk (Birmingham) |
| Employment type | employed |

### Income

| Field | Value |
|---|---|
| Monthly net income | £2,750 |

### Spending

| Category | Monthly £ | Confidence |
|---|---|---|
| Housing | £780 | high (rent, direct debit) |
| Bills | £260 | medium |
| Transport | £180 | medium |
| Food | £360 | medium |
| Fun | £220 | medium |
| Subscriptions | £65 | high |
| Other | £70 | low |
| **Total** | **£1,935** | |

### Current monthly savings

| Field | Value |
|---|---|
| current_monthly_savings_gbp | £100 |

### Debt minimum payments

| Debt | Monthly minimum |
|---|---|
| Credit card | £120 |
| BNPL | £0 (not actively repaying beyond balance) |
| Student loan | Deducted from payslip (not captured here) |

### Assets

| Asset | Value |
|---|---|
| Cash savings | £1,200 |
| ISA | £0 |
| Pension | £5,500 (workplace DC, estimated) |
| Investments | £0 |
| Property equity | £0 |
| joint_share_noted | false |

### Debts

| Type | Balance | APR | Monthly minimum | high_interest |
|---|---|---|---|---|
| credit_card | £5,500 | 24.9% | £120 | **true** |
| bnpl | £600 | 0% (promotional) | £0 | false |
| student_loan | £22,000 | ~5.5% (Plan 2) | payroll deduction | false |

*Student loan is a functional graduate tax — captured but not flagged as high-interest.*

### Goals

| Field | Value |
|---|---|
| Target retirement age | 55 |
| Target lifestyle | comfortable |

---

## Expected output characteristics

### Persona and framing
- Detected as **P1** — "sort of" is vague; FIRE explainer delivered.
- Both disclaimers present.
- Tone: neutral throughout; no judgement about the credit card balance.

### Leak
- Total spending: £1,935
- Debt minimum payments: £120 (credit card only; student loan is payroll-deducted)
- Monthly capacity: £2,750 − £1,935 − £120 = **£695**
- Leak: £695 − £100 = **~£595/month**
- `leak_low_confidence` = false (only 1 category low-confidence)
- `leak_caveat_required` = **true** — high-interest debt present
- Leak caveat must appear: *"This figure does not account for payments needed to address high-interest debts — those should be considered separately."*

### Key calculated outputs
- Savings rate: ~3.6% (£100 / £2,750)
- FIRE number: **£750,000** (£30,000 × 25)
- Portfolio (ex-property): £6,700
- Projected FI age: **very far out** — expect 70+ or "not reachable at current rate"

### Flags
- `high_interest_debt_total_gbp` = £5,500
- `high_interest_blended_apr` = 24.9% (only one qualifying debt)
- High-interest debt section must fire with neutral observation — **no prescription on what to do**
- BNPL and student loan appear in snapshot but not in high-interest section

### Expected actions (top candidates)
- Leak: ~£595/month capacity (with caveat)
- High-interest debt: neutral observation — £5,500 at 24.9% APR affects the projection; worth having separately in view
- Pension match check: should fire (employed)

### Levers
- Extra £200/month: some FI age improvement; gap remains large
- Retire at 60 instead of 55: likely narrows gap materially
- Lifestyle downshift: comfortable → modest reduces FIRE number from £750,000 to £450,000 (saves £300,000)

### Compliance checklist
- [ ] Opening disclaimer present
- [ ] Closing disclaimer present
- [ ] Leak caveat present (high-interest debt)
- [ ] High-interest debt section: neutral observation only, no "you should pay this off"
- [ ] No specific products named
- [ ] No buy/sell language
- [ ] FI age delivered as honest number (70s is 70s)
- [ ] No moralising about credit card or spending level
