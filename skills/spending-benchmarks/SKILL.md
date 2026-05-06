---
name: spending-benchmarks
description: Provides UK monthly household spending benchmark ranges by category, income band, and broad region (London vs rest of UK). Use this skill whenever the fire-ready interview encounters an "I don't know" answer for a spending category, or whenever you need to sanity-check a user's stated spending against typical UK figures. Returns ranges, not point estimates, so the user can place themselves on the distribution.
---

# UK Spending Benchmarks

Provides ballpark monthly £ ranges for spending categories used in the FIRE Ready interview. Based on UK ONS Family Spending data, with a London uplift for housing-sensitive categories.

## When to use

- A user gives "I don't know" or hesitates on a spending category in the FIRE Ready interview.
- You want to sanity-check whether a user's stated number is materially out of line with typical UK figures (e.g., they report £100/month total housing in London — almost certainly an error or omission).

## How to use

1. Read the user's monthly net income and (if known) location (London vs rest of UK).
2. Look up the relevant range in `references/uk-ons-benchmarks.md`.
3. Offer the range conversationally: *"For someone on your income in [region], [category] typically lands between £X and £Y a month — does that feel right, or higher/lower?"*
4. Capture the user's response with confidence flag `low`.

## Important rules

- **These are population averages.** Never imply that a user's own spending "should" match the benchmark. The benchmark is a memory aid for "what's plausible", not a target.
- **Don't volunteer benchmarks unprompted.** Only use them as a fallback when the user genuinely doesn't know.
- **Note vintage in any output that uses them.** The reference data should be refreshed against the latest ONS Family Spending publication annually.
- **Ranges, not points.** Always offer a range, not a single number — the point is to anchor the user, not pretend at precision.

## MVP data caveat

The reference file ships with **placeholder figures** based on rough UK averages. Refresh against the latest ONS Family Spending publication before any non-MVP use. The skill should surface a "data vintage: [year]" note in the final output when these figures contributed materially (i.e., more than half of categories were estimated from benchmarks).

## Reference data

See `references/uk-ons-benchmarks.md` for the lookup table.
