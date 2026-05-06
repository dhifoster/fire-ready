# UK Spending Benchmarks — Reference Data

**Data vintage:** MVP placeholders, derived from approximate UK ONS Family Spending averages. Refresh against the latest ONS publication before production use.

**Source for production refresh:** [ONS Family spending in the UK](https://www.ons.gov.uk/peoplepopulationandcommunity/personalandhouseholdfinances/expenditure/bulletins/familyspendingintheuk/latest)

---

## How to read this table

Pick the user's income band and region, then offer the range for the relevant category. The ranges represent rough middle-50% bands — most people land inside, some land outside.

**Income bands** (monthly net take-home for a single adult):
- **Low:** under £2,000/month
- **Mid-low:** £2,000–£3,000
- **Mid:** £3,000–£4,500
- **Mid-high:** £4,500–£6,500
- **High:** £6,500+

**Regions:**
- **London** (and inner SE England with similar housing costs)
- **Rest of UK**

---

## Housing (rent or mortgage payment + service charges)

This is the category with the largest London uplift.

| Income band | Rest of UK | London |
|---|---|---|
| Low | £400–£700 | £700–£1,100 |
| Mid-low | £550–£900 | £900–£1,400 |
| Mid | £700–£1,200 | £1,200–£1,800 |
| Mid-high | £900–£1,500 | £1,500–£2,500 |
| High | £1,200–£2,200 | £2,000–£4,000+ |

## Bills (utilities, council tax, internet, phone, insurance)

Less regional variation — council tax bands vary, but utilities are similar nationally.

| Income band | Range |
|---|---|
| Low | £150–£280 |
| Mid-low | £200–£350 |
| Mid | £250–£420 |
| Mid-high | £300–£500 |
| High | £350–£600 |

## Transport (commute + fuel + public transport + car costs)

Higher in London for commuters but lower for non-drivers. Use judgement.

| Income band | Rest of UK | London |
|---|---|---|
| Low | £80–£200 | £100–£250 |
| Mid-low | £150–£300 | £180–£350 |
| Mid | £200–£400 | £220–£450 |
| Mid-high | £250–£500 | £280–£550 |
| High | £300–£700 | £350–£800 |

## Food (groceries + eating out + delivery + coffees)

The single biggest category most people underestimate. Eating out and delivery push this up fast.

| Income band | Rest of UK | London |
|---|---|---|
| Low | £180–£320 | £220–£400 |
| Mid-low | £250–£450 | £300–£550 |
| Mid | £350–£600 | £400–£700 |
| Mid-high | £450–£800 | £500–£900 |
| High | £600–£1,200 | £700–£1,400 |

## Fun (entertainment, hobbies, social, holidays, gifts)

Very income-elastic. Holidays in particular skew upward when annualised.

| Income band | Range |
|---|---|
| Low | £80–£200 |
| Mid-low | £150–£350 |
| Mid | £250–£550 |
| Mid-high | £400–£800 |
| High | £600–£1,500+ |

## Subscriptions (streaming, gym, software, anything recurring)

The "death by a thousand cuts" category. Worth mentioning to the user that this is the easiest place to find leaks.

| Income band | Range |
|---|---|
| Low | £20–£80 |
| Mid-low | £40–£120 |
| Mid | £60–£180 |
| Mid-high | £80–£250 |
| High | £100–£400 |

## Other (anything else recurring — childcare, pet costs, donations, etc.)

Hugely variable. Default to asking the user what they have in mind rather than offering a range.

| Income band | Range (excluding childcare) |
|---|---|
| Any | £30–£300 |

Childcare in particular can be £1,000–£2,500/month per child in London — never roll into "other", capture separately.

---

## Sense-check thresholds

If a user-stated number is well outside the relevant band (less than 50% of the lower bound or more than 200% of the upper bound), it's worth gently asking *"that's lower/higher than typical — does that include [X]?"* before accepting the figure. Don't push hard. The point is to catch obvious omissions (forgotten council tax, unaccounted-for service charge), not to police their lifestyle.
