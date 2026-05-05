---
name: report-generator
description: Formats the final FIRE Ready snapshot output for chat rendering. Use this skill at the end of the fire-ready interview, after fire-calculations has computed the numbers, to produce the copy-able structured snapshot the user takes away. Owns the output template, action selection, and tone rules. Never recommends specific products, funds, or providers.
---

# Report Generator — FIRE Ready Snapshot

Produce the final user-facing snapshot from the populated data model and the calculations output.

## Inputs

- The populated data model from `fire-ready` (income, spending, assets, liabilities, goals).
- The output of `fire-calculations` (savings rate, leak, FIRE number, projected FI age, levers, flags).

## Output template

Render this in chat. Substitute values in `{}`. Apply conditional sections based on the flags.

```
---

# Your FIRE Ready Snapshot

## 💰 Your leak: £{leak_gbp_per_month}/month

This is the gap between what you're capable of saving and what you currently are.

{IF leak_low_confidence:}
*Estimate based on benchmark ranges — refine with your bank statements for higher precision.*
{END IF}

{IF leak_caveat_required:}
*This figure does not account for payments needed to address high-interest debts — those should be considered separately.*
{END IF}

## 📊 The numbers

| Metric | Value |
|---|---|
| Current savings rate | {savings_rate_percent}% |
| FIRE number | £{fire_number_gbp:,} |
| Projected FI age | {projected_fi_age} |

{IF negative_savings_rate:}
> ⚠️ **You're currently spending more than you earn.** This needs to be addressed before FIRE math becomes meaningful. The numbers above assume your spending stays where it is — anything you can pull back, in any category, moves the picture forward.
{END IF}

{IF high_interest_debt_total_gbp > 0:}
## ⚠️ High-interest debt flag

You have £{high_interest_debt_total_gbp:,} in debt at a blended APR of {high_interest_blended_apr}%. High-interest debt materially affects the FIRE projection — worth considering separately from the savings picture above.
{END IF}

## 🎯 Three things you could do this week

1. {action_1}
2. {action_2}
3. {action_3}

## 🎚️ What would change the picture

| Lever | Impact |
|---|---|
| Save an extra £200/month | FI age moves from {projected_fi_age} to {levers.extra_savings_200.new_fi_age} |
| Save an extra £500/month | FI age moves from {projected_fi_age} to {levers.extra_savings_500.new_fi_age} |
{IF downshift available:} | Shift target lifestyle from {current_band} to {new_target_band} | FIRE number drops from £{fire_number_gbp:,} to £{new_fire_number_gbp:,} (saves £{delta_gbp:,}) | {END IF}

---

*This is an educational tool, not regulated financial advice. I'm an AI and I can make mistakes — please double-check anything important against your own records or with a qualified advisor. For regulated advice, consider an FCA-authorised advisor: https://register.fca.org.uk/*
```

## Generating the three actions

This is the most delicate part of the output. The actions must:
- Frame as **capacity**, never advice.
- Reference categories and behaviours only — never specific products, funds, or providers.
- Be ranked by relevance to the user's snapshot (highest impact first).

### Action templates

Pick three from this set, ranked by relevance. Always pick three. If fewer than three apply meaningfully, pad from the lower-priority templates rather than forcing an irrelevant one.

**Priority order when selecting:**

1. **Negative savings rate** (if `negative_savings_rate`):
   *"Your spending currently exceeds your income. The first action is identifying where this is happening — a 30-minute review of your last month's bank statements will surface most of it."*

2. **Leak present** (if `leak_gbp_per_month > 0`):
   *"You have £{leak_gbp_per_month}/month uninvested that you appear to have capacity for — consider directing this into an investment product of your choice. A tax-advantaged wrapper such as an ISA or pension is worth understanding before deciding where it goes."*

3. **High-interest debt** (if `leak_caveat_required`):
   *"You have £{high_interest_debt_total_gbp:,} in high-interest debt. Worth reviewing the interest cost in pounds — at {high_interest_blended_apr}% APR, this is a meaningful drag worth addressing."*

4. **Subscription leak** (if `subscriptions.monthly_gbp > 0.05 * monthly_net_income`):
   *"Your subscriptions total £{subscriptions}/month — about {pct}% of your take-home. A 10-minute audit usually finds 1–2 you've forgotten you're paying for."*

5. **Discretionary leak** (if `(food + fun) > 0.30 * monthly_net_income`):
   *"Discretionary spending (food + fun) is £{x}/month — about {pct}% of your take-home. The biggest savings leaks usually surface here, not in subscriptions."*

6. **Pension match check** (always, unless user is self-employed):
   *"If you're employed, check whether you're getting the full employer pension match. Unmatched employer contribution is the most expensive thing to leave on the table."*

7. **Emergency fund** (if `cash_savings < 3 * (housing + bills + transport + food)`):
   *"Your cash buffer covers about {n} months of essential spending — the common rule of thumb is 3–6 months. Worth knowing where you sit."*

8. **FI gap framing** (if `projected_fi_age > target_retirement_age + 5`):
   *"There's a gap between when you'd like to retire ({target}) and the current trajectory ({projected}). The two main levers are saving more or aiming for a lower target lifestyle — see the panel below for the rough trade-offs."*

### Avoid

- ❌ Specific platforms, providers, funds, or products ("open a Trading 212 ISA", "Vanguard FTSE Global", "switch to Monzo")
- ❌ Allocations ("60% equities, 40% bonds")
- ❌ Buy/sell language
- ❌ "You should..." or "You need to..." phrasing
- ❌ Moralising about lifestyle choices ("eating out so much is hurting you")

## Tone rules

- **Sobering on the FI age.** Don't soften the number. If projected FI age is 78, the snapshot says 78. The lever panel does the optimistic work.
- **Optimistic on the leak and levers** — frame as agency.
- **Never moralising about spending choices.** The user is an adult.
- **Always descriptive, never prescriptive.** "Your numbers say...", "consider...", "worth knowing..." — never "you should".
- **Use £ consistently.** Round numbers (£4,800 not £4,832).
- **Use thousands separators.** £1,250,000 not £1250000.

## Final disclaimer

Always end with:

> *This is an educational tool, not regulated financial advice. I'm an AI and I can make mistakes — please double-check anything important against your own records or with a qualified advisor. For regulated advice, consider an FCA-authorised advisor: https://register.fca.org.uk/*

This is non-negotiable. It appears every time.
