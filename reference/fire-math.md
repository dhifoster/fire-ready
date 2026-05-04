# FIRE math

Load this file when you need to compute the snapshot. All formulas below assume real (inflation-adjusted) returns, so the numbers Claude shows the user are in today's pounds.

## Core formulas

### FIRE number

```
fireNumber = annualSpendInRetirement / (withdrawalRatePct / 100)
```

Default `withdrawalRatePct = 4` (the Trinity / Bengen rate). Allow the user to override:
- **Lean FIRE / cautious** — 3.25%
- **Standard FIRE** — 4% (default)
- **Coast FIRE / aggressive** — 4.5%

If the user hasn't told you their target retirement spend, use this fallback:
```
annualSpendInRetirement = currentMonthlySpend × 12 × spendMultiplier
```
where `spendMultiplier`:
- 0.7 for "I plan to live more simply in retirement" or "Lean"
- 1.0 for "Similar to today" or "Standard" (default)
- 1.3 for "I want to travel / live well" or "Fat"

Always tell the user which multiplier you used and offer to change it.

### Years to FIRE

Solve for `n` in the future-value-of-an-annuity formula:

```
FV = PV × (1 + r)^n + PMT × [((1 + r)^n - 1) / r]
```

Where:
- `FV` = `fireNumber`
- `PV` = `currentInvestedNetWorth` (cash + pensions + ISAs/GIAs + other liquid assets; **exclude residential property** unless the user explicitly says they plan to downsize)
- `PMT` = annual savings = `(monthlySurplus × 12) + annualPensionContributions`
- `r` = real return assumption — default **5%** (`0.05`). Allow override 3–7%.

Closed-form solution:
```
n = log((FV × r + PMT) / (PV × r + PMT)) / log(1 + r)
```

If `PMT = 0` (the user isn't saving), simplify to:
```
n = log(FV / PV) / log(1 + r)
```

Round `n` up to the nearest year. `projectedAgeAtFire = ageNow + n`.

If `n > 50`, cap it at 50 and flag this in the recommendations as "current trajectory does not converge to your target — the most impactful change is increasing your savings rate."

### Trajectory

For each year from now (`y = 0`) to `n + 5`, compute:
```
netWorth[y] = PV × (1 + r)^y + PMT × [((1 + r)^y - 1) / r]
```

Emit as `[{ year: 2026 + y, netWorth: ... }, ...]`. This gives the chart on the report a smooth compounding curve that crosses the FIRE line at year `n`.

## RAG thresholds

### Savings rate (`monthlySurplus / monthlyNetIncome`)
- 🟢 GREEN: ≥ 25%
- 🟡 AMBER: 10–24%
- 🔴 RED: < 10%

Note: include pension contributions in the numerator if treating them as savings (they are). So `effectiveSavingsRate = (monthlySurplus + monthlyPensionContributions) / grossIncome`.

### Emergency fund (`cash / monthlySpend`)
- 🟢 GREEN: ≥ 6 months
- 🟡 AMBER: 3–5 months
- 🔴 RED: < 3 months

Cash here means accessible cash + cash-equivalent pots, NOT cash-ISA or pension money that's hard to reach in an emergency.

### Investment allocation (heuristic — flag if cash-heavy and long horizon)

For users with a horizon ≥ 10 years to FIRE:
- 🟢 GREEN: cash ≤ 10% of investable net worth
- 🟡 AMBER: cash 10–20%
- 🔴 RED: cash > 20%

For users with a horizon < 10 years, lower thresholds (more cash is sensible as the horizon shortens).

This is a *heuristic*, not advice. The recommendation, if RED, is "consider whether some of this could be working harder for you" — never name a product.

### Trajectory (`projectedAgeAtFire` vs `ageTarget`)
- 🟢 GREEN: `projectedAgeAtFire ≤ ageTarget`
- 🟡 AMBER: `projectedAgeAtFire ≤ ageTarget + 5`
- 🔴 RED: `projectedAgeAtFire > ageTarget + 5`

## Recommendations — selection rules

Generate at most three. In priority order:

### 1. Surplus cash deployment
**Trigger:** `cash > 6 × monthlySpend` AND ISA/SIPP allowances likely unused (proxy: ISAs/GIAs balance < £20k contributed this tax year, or pension contributions < £60k).

**Title format:** *"Your cash buffer looks healthy — your tax allowances might be working harder."*

**Rationale must mention:** the £20,000 annual ISA allowance, or the £60,000 / 100% of relevant earnings annual SIPP allowance, framed as **allowances** the user may have unused capacity for. Never name a product or platform.

**Expected impact:** "Using unused tax allowances on excess cash could meaningfully accelerate the trajectory — the gain depends on the difference between cash interest and your assumed return."

### 2. Spending category outlier
**Trigger:** Any category in `spendByCategory` where `monthly > 1.25 × benchmarkMonthly`.

**Title format:** *"Your [Category] spend is ~[X]% above the UK average for your household."*

**Rationale must mention:** the benchmark, the user's number, and that this isn't necessarily a problem (people choose to spend more on what matters to them) — but if it's not a deliberate priority, here's where the surplus could come from.

**Expected impact:** Calculate: bringing this category to benchmark would free up `(monthly - benchmark) × 12` annually. If redirected to investments at the user's `r`, project the impact on `yearsToFire`.

### 3. Trajectory accelerator
**Trigger:** Always relevant, but pick the highest-leverage one for this user.

Options to consider:
- Capture full employer pension match (if user is leaving any on the table — ask if you don't know).
- Increase pension contributions by a specific %.
- Increase savings rate by a specific £/month.

**Title format:** *"Adding £[X]/month to investments could bring your FIRE date forward by ~[Y] years."*

**Rationale must mention:** the specific £ amount, where it would come from (the surplus cash, the category outlier, or unallocated income), and the resulting change in `projectedAgeAtFire`.

**Expected impact:** Re-run the years-to-FIRE formula with the new PMT and report the delta in years.

## Sanity checks before emitting the snapshot

Before you output the JSON, mentally verify:

- [ ] `monthlySurplus = monthlyNetIncome - monthlySpend` (within £1 rounding).
- [ ] `netWorth.total = sum of assets - sum of liabilities`.
- [ ] `fireNumber > £100,000` (if not, you've probably misunderstood the spend figure).
- [ ] `yearsToFire` is between 0 and 60.
- [ ] `trajectory[0].netWorth ≈ currentInvestedNetWorth`.
- [ ] `trajectory[yearsToFire].netWorth ≥ fireNumber`.
- [ ] Each RAG flag matches the threshold rule above.

If any check fails, re-state the inputs to the user and ask them to confirm before proceeding.
