# Interview script

A conversational, stage-by-stage script. Read this when running the interview. Don't deliver these as a checklist — pick the variant that matches the tone the user has set, and weave the questions into a natural conversation.

## Opening

Always open with the disclaimer (see SKILL.md). Then:

> "Right — to give you a useful picture, I'll need to ask you a few questions. It'll take 8–10 minutes, and you can stop at any point. Sound good?"

Wait for assent before continuing.

## Stage 1 — Lifestyle context

Aim: understand who they are, when they want to retire, and what shape that retirement looks like.

**Questions to cover:**

1. *"How old are you, and is anyone else's finances tied to yours — partner, kids, anyone you support?"* → captures `ageNow`, `household`.

2. *"Where in the UK are you based? London changes the maths a lot."* → captures `region`. (If they say outside the UK, note that the ONS benchmarks may not apply and ask how they want to handle that.)

3. *"When would you ideally like the option to stop working — even if you wouldn't actually stop?"* → captures `ageTarget`. The "option to stop" framing is important; it captures Coast FIRE intent without forcing a Lean/Standard label yet.

4. *"And in retirement — picture it for a second — are you living simply, similarly to now, or living well? No wrong answer."* → captures FIRE flavour for the spend multiplier.

5. *"Anything big on the horizon that'll change your finances? House move, kids, sabbatical, leaving a job?"* → optional context, may affect recommendations.

## Stage 2 — Income & savings rate

Aim: understand the inflows and how much is being routed to investments versus consumption.

**Questions to cover:**

1. *"What's your take-home pay — what hits your account each month after tax and pension?"* → captures `monthlyNetIncome`.

2. *"And on pensions — what % of your salary goes in from you, plus what your employer adds?"* → captures `monthlyPensionContributions`. Convert percentage to £ using their stated salary if needed; ask gross salary if they only know percentages.

3. *"Anything else coming in monthly? Side income, dividends, rental, partner's contributions to shared bills?"* → captures `otherIncome` if material.

If the user's pension setup sounds non-standard (DB pension, NHS pension, civil service), ask for the projected annual income at retirement instead of trying to value it as a pot. Note this in the snapshot as `dbPensionIncome` and subtract it from `annualSpendInRetirement` when computing `fireNumber`.

## Stage 3 — Net worth

Aim: get the asset and liability picture. Use Monzo data where available; ask for everything else.

**Questions to cover:**

1. *"Cash — what's sitting in current accounts and easy-access savings, including any pots? I can see Monzo if you've connected it."* → captures `cash`. Cross-reference Monzo balances if available.

2. *"Pension pots — what's the total across all your DC pensions? An estimate is fine; check your Pension Tracing Service if you've lost track of old ones."* → captures `pensions`.

3. *"ISAs and any other investments? Stocks & Shares ISA, GIA, Lifetime ISA?"* → captures `isasGia`. Treat LISA as ISA for this purpose; flag separately if the user is using LISA for retirement (penalty on withdrawal before 60).

4. *"Property — do you own where you live? If so, roughly what's the equity (value minus what's left on the mortgage)? Any buy-to-lets?"* → captures `propertyEquity`. Ask explicitly: "When we project FIRE, do you want to count this property's value, on the assumption you'd downsize? The default is no — we treat it as where you live."

5. *"Debts — student loan, credit cards, car finance, anything else?"* → captures `otherDebt`. Student loan should generally be excluded from FIRE calculations because it's income-contingent in the UK; mention this in the Recommendations if relevant.

## Stage 4 — Spending

**If Monzo is connected and you've already aggregated:** skip the open question and go straight to confirmation.

> "OK, from your Monzo I'm seeing roughly: [Housing X, Groceries Y, Transport Z, Discretionary W]. Does that line up with how you experience it?"

Pause for adjustments. The user often knows that "Monzo doesn't include the joint mortgage account" or similar; capture those as additions.

**If Monzo is NOT connected:**

1. *"What does a typical month of spending look like in total?"* → captures `monthlySpend`.

2. *"And of that, what are your three biggest categories? Even a rough sense is fine."* → captures top three categories. Allocate the remainder to "Other" and a sensible distribution across the standard categories — but be explicit that this is an estimate.

3. *"Any one-off costs coming up I should account for? Holiday, big purchase, wedding?"* → captures unusual items.

## Stage 5 — Risk & confirmation

Aim: capture risk tolerance (affects the recommendation tone) and confirm everything before computing.

**Questions to cover:**

1. *"On investments — when markets drop 30%, are you the kind of person who panics and sells, holds, or buys more?"* → maps to risk tolerance: panic = Cautious, hold = Balanced, buy = Adventurous. Don't ask it formally; the answer informs how you frame Recommendation #1.

**Then read everything back:**

> "OK, let me check I've got this right. You're [age], living in [region] with [household]. You'd like the option to stop working at [ageTarget], living [Lean/Standard/Fat]. You bring in £[X]/month after tax, with £[Y]/month going into pensions. Your net worth is £[total] — that's £[cash] cash, £[pensions] in pensions, £[isasGia] in ISAs, £[propertyEquity] in property equity, less £[debt] in debt. You're spending £[X]/month, leaving you about £[surplus]. Have I got that right?"

Wait for explicit confirmation. If the user corrects anything, update and re-confirm. **Don't compute the snapshot without explicit assent at this step.**

## Tonal guidance throughout

- **Match the user's energy.** If they're terse, be terse. If they're chatty, be chatty.
- **Never moralise about spending.** "£800 on restaurants" is not a problem to solve; it's data. The report can flag it as above benchmark; you don't need to.
- **Use round numbers in your read-backs** — "around £85k in pensions" is friendlier than "£84,623 in pensions" and signals you're working at a sensible level of precision.
- **Don't ask follow-ups that aren't going to change the output.** If the user gives you "around £2k/month spending", don't ask them to break it down to the pound. Use the Monzo split if you have it; otherwise ask for the top three categories and move on.
- **If the user says they don't know something**, give them a way to estimate ("about how much?", "is it more like £10k or £100k?"). Don't make them log into another app mid-conversation.
