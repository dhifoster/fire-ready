# UK ONS Family Spending benchmarks

Source: UK Office for National Statistics, Family Spending Survey, Financial Year Ending 2023 (most recent published as of skill creation). Update annually when ONS releases new data.

These are **average UK household monthly spending** figures by category. Use them as the comparator in the report's "Spend vs benchmark" section.

When checking benchmarks, match the closest household type to what the user described in Stage 1 of the interview.

## Single adult under 65

| Category | £ / month |
|---|---|
| Housing (rent, mortgage interest, utilities) | £680 |
| Food & groceries | £210 |
| Transport (incl. fuel & public transport) | £260 |
| Discretionary (recreation, restaurants, hotels) | £290 |
| Communication (phone, broadband) | £55 |
| Clothing & footwear | £75 |
| Household goods & services | £130 |
| Health (private/non-NHS) | £30 |
| Insurance & misc | £130 |
| Education | £15 |
| **Total** | **~£1,875** |

## Couple, no children

| Category | £ / month |
|---|---|
| Housing | £840 |
| Food & groceries | £390 |
| Transport | £450 |
| Discretionary | £540 |
| Communication | £80 |
| Clothing & footwear | £125 |
| Household goods & services | £210 |
| Health | £55 |
| Insurance & misc | £200 |
| Education | £20 |
| **Total** | **~£2,910** |

## Family with children

| Category | £ / month |
|---|---|
| Housing | £960 |
| Food & groceries | £530 |
| Transport | £510 |
| Discretionary | £550 |
| Communication | £95 |
| Clothing & footwear | £175 |
| Household goods & services | £240 |
| Health | £55 |
| Insurance & misc | £230 |
| Education (incl. childcare) | £180 |
| **Total** | **~£3,525** |

## Retired household (single, 65+)

For users who want to project their retirement spending against actual retired-household averages.

| Category | £ / month |
|---|---|
| Housing | £450 |
| Food & groceries | £210 |
| Transport | £180 |
| Discretionary | £250 |
| Communication | £55 |
| Clothing & footwear | £55 |
| Household goods & services | £130 |
| Health | £55 |
| Insurance & misc | £140 |
| **Total** | **~£1,525** |

## Retired household (couple, 65+)

| Category | £ / month |
|---|---|
| Housing | £510 |
| Food & groceries | £390 |
| Transport | £330 |
| Discretionary | £450 |
| Communication | £80 |
| Clothing & footwear | £100 |
| Household goods & services | £200 |
| Health | £85 |
| Insurance & misc | £225 |
| **Total** | **~£2,370** |

## Notes for using these benchmarks

- **Housing excludes mortgage capital repayment.** The ONS treats mortgage capital as savings, not spending. If the user reports their full mortgage payment, separate the interest portion (use ~30% of payment as a rough split for a typical 5-year-old mortgage; ask the user if precision matters).
- **Council tax is in housing**, not insurance.
- **London users will be 25–40% above national housing benchmark.** Flag this in the report rather than treating it as overspending. Don't recommend "move out of London" — that's a life decision, not a financial one.
- **Childcare is enormous and uneven.** If the user has a child under 5 and reports £1,000+ on childcare, do NOT flag this as "above benchmark" — flag it as "this is a temporary line item; trajectory will improve when childcare ends."
- **Categories are normalised to the schema's nine buckets.** When categorising Monzo transactions, map merchant categories to these nine. Use the merchant name and Monzo category as inputs; ask the user to confirm any large or ambiguous category.

## How to display in the report

For each of the user's reported spending categories, render a horizontal bar showing:
- The user's monthly figure (filled bar in the accent colour).
- The benchmark figure (a thin grey reference line at the benchmark position).
- A label with the £ amount and the % above/below benchmark.

If a category is more than 25% above benchmark, colour the bar amber. If more than 50% above, colour it red. Below or at benchmark, leave it accent-coloured.
