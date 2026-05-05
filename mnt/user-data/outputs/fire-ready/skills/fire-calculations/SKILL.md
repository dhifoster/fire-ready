---
name: fire-calculations
description: Computes FIRE readiness metrics from a user's financial snapshot — savings rate, savings leak, FIRE number, time to FI, and lever scenarios (extra savings / shifted retirement age / different lifestyle). Use this skill whenever the fire-ready interview has finished collecting data and needs the numbers computed for the snapshot. Pure functions — no conversational logic.
---

# FIRE Calculations

Compute the numerical outputs for the FIRE Ready snapshot. Pure functions. No conversation.

If a code execution tool is available in the runtime, use it for the time-to-FI calculation — iterative numerical solving is more reliable than algebraic rearrangement done by hand. If not, compute year-by-year and report the year the portfolio crosses the FIRE number.

## Input

A populated snapshot data model from the `fire-ready` skill (see `fire-ready/SKILL.md` for the full shape).

## Output

```json
{
  "savings_rate_percent": number,
  "current_monthly_savings_gbp": number,
  "monthly_capacity_gbp": number,
  "leak_gbp_per_month": number,
  "fire_number_gbp": number,
  "current_portfolio_gbp": number,
  "projected_fi_age": number,
  "high_interest_debt_total_gbp": number,
  "high_interest_blended_apr": number,
  "leak_caveat_required": boolean,
  "leak_low_confidence": boolean,
  "negative_savings_rate": boolean,
  "levers": {
    "extra_savings_200": { "new_fi_age": number },
    "extra_savings_500": { "new_fi_age": number },
    "retire_5_years_later": { "delta_years_to_target": number },
    "downshift_lifestyle": {
      "new_fire_number_gbp": number,
      "delta_gbp": number,
      "new_target_band": "modest" | "comfortable"
    }
  }
}
```

## Formulas

### Savings rate and leak

```
total_spending = sum(spending categories)
monthly_capacity = monthly_net_income - total_spending
current_monthly_savings = explicitly captured if available, else assume 0
savings_rate_percent = (current_monthly_savings / monthly_net_income) * 100
leak_gbp_per_month = monthly_capacity - current_monthly_savings
```

If `monthly_capacity < 0`: set `negative_savings_rate = true`. Don't proceed with FIRE projections as if they're meaningful — the report-generator will surface this as a critical insight instead.

### FIRE number

Lifestyle band → annual spend mapping (MVP):
- `modest` → £18,000/year
- `comfortable` → £30,000/year
- `generous` → £50,000/year

If the user gave an explicit `target_annual_spend_gbp`, use that; otherwise use the band mapping.

```
fire_number_gbp = target_annual_spend_gbp * 25
```

### Current portfolio

Liquid wealth that compounds toward FIRE — **excludes** property equity (not income-producing in the same way and creates accommodation cost confusion if drawn down).

```
current_portfolio_gbp = cash_savings + isa + pension + investments
```

### Projected FI age

Use a **5% real annual return** assumption (conservative post-inflation expectation for a global equity portfolio).

Solve for years `n` such that:

```
current_portfolio * (1+r)^n + monthly_savings * 12 * ((1+r)^n - 1) / r = fire_number
```

Where:
- `r = 0.05`
- `monthly_savings` = `current_monthly_savings` (project at their actual rate, not aspirational capacity)

```
projected_fi_age = current_age + n
```

If `current_monthly_savings = 0` and `current_portfolio = 0`: `projected_fi_age = "never at current rate"`. Surface this honestly — the lever panel becomes the main story.

If `n > 100`: same — report as "not reachable at current rate". Don't print a meaningless 200-year projection.

### High-interest debt

```
high_interest_debts = filter(liabilities, d => d.apr_percent > 10)
high_interest_debt_total_gbp = sum(d.balance_gbp for d in high_interest_debts)
high_interest_blended_apr = sum(d.balance * d.apr) / sum(d.balance)
leak_caveat_required = high_interest_debt_total_gbp > 0
```

### Low-confidence flag

```
low_conf_count = count(spending categories with confidence == "low")
leak_low_confidence = low_conf_count > 3   // more than half of 7 categories
```

## Levers

Three scenarios. Each holds everything else constant and varies one input.

### Lever 1 — Extra savings (£200/month and £500/month)

Recompute time-to-FI with `monthly_savings + 200` and again with `monthly_savings + 500`. Report new projected FI age for each.

```
levers.extra_savings_200.new_fi_age = current_age + solve_n(monthly_savings + 200, ...)
levers.extra_savings_500.new_fi_age = current_age + solve_n(monthly_savings + 500, ...)
```

### Lever 2 — Retire 5 years later

This lever frames "what if you accept retiring 5 years later than your target". Compute the gap between projected FI age and (target_retirement_age + 5):

```
levers.retire_5_years_later.delta_years_to_target = projected_fi_age - (target_retirement_age + 5)
```

If positive: still a gap even with the extra 5 years. If zero or negative: pushing 5 years closes the gap entirely. The report-generator will phrase this contextually.

### Lever 3 — Downshift lifestyle by one band

Compute new FIRE number for the band one step lower:
- generous → comfortable
- comfortable → modest
- modest → modest (no further downshift; report null and let report-generator hide the row)

```
new_target_band = downshift(goals.target_lifestyle)
new_target_annual_spend = band_to_annual(new_target_band)
new_fire_number_gbp = new_target_annual_spend * 25
delta_gbp = fire_number_gbp - new_fire_number_gbp
```

## Output rounding

- £ figures: round to nearest £100. (FIRE numbers can be rounded to nearest £1,000.)
- Percentages: round to nearest 0.5%.
- Ages: round to nearest year.
- APRs: round to nearest 0.5%.

## Sanity checks before returning

- If `leak_gbp_per_month` is negative (user is saving more than capacity suggests, e.g., they reported some saving but their stated spending leaves no surplus): set `leak_gbp_per_month = 0` and flag in a comment. This usually means the user under-reported some spending category, and the negative leak is a calculation artefact.
- If `fire_number_gbp` < 100,000: double-check the lifestyle band wasn't entered as a per-month figure. £18k/year is realistic; £18k FIRE number is not.
- If `projected_fi_age < 30`: double-check the inputs — almost certainly something was input wrong.
