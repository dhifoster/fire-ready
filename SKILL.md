---
name: fire-ready
description: Use this skill when the user wants to assess their FIRE (Financial Independence, Retire Early) readiness, check if they're on track to retire, get a retirement projection, or understand how their finances compare to UK national benchmarks. Triggers include phrases like "FIRE check", "am I on track to retire", "retirement readiness", "how much do I need to retire", "FIRE number", or "retirement projection". The skill conducts a structured interview, optionally pulls real data from Monzo via the monzo-mcp server if connected, and produces a one-page HTML report saved to disk.
---

# FIRE Ready

> Everyone should be ready to retire.

A guided 10-minute conversation that gives the user a one-page snapshot of their progress towards Financial Independence / Retire Early (FIRE), grounded in their own numbers and benchmarked against UK national data.

## When to use this skill

Invoke when the user asks any version of "am I on track to retire?", asks for a FIRE check, or asks how to think about retirement readiness using their own data.

Do NOT invoke for:
- Specific investment product picks (refuse — see `reference/guardrails.md`).
- Tax planning, debt restructuring, or insolvency advice.
- General market commentary or speculation.

## Hard rule before anything else

This skill produces **information, not regulated financial advice**. Before computing anything, before pulling any data, the very first message in any conversation that uses this skill must include:

> "Quick note before we start: this is a personal finance information tool, not regulated financial advice. I'll help you see your numbers clearly and compare them to UK benchmarks. For decisions about specific products, pensions, or tax structuring, please speak to an FCA-authorised advisor. You can find one at unbiased.co.uk or vouchedfor.co.uk."

If the user at any point asks "should I buy X fund / put my money in Y / move my pension to Z", refuse the specific recommendation. Always re-ground in: *information, not advice.* See `reference/guardrails.md` for the full refusal taxonomy.

## Setup verification

Before starting the interview, silently check what tools are available:

1. **Is the Monzo MCP loaded?** Look for tools whose names suggest Monzo (typically prefixed with the server name, e.g. `monzo__*`). The skill is designed to work with [samaxbytez/monzo-mcp](https://github.com/samaxbytez/monzo-mcp), which exposes balance, transactions, and pots tools.
2. **Is the filesystem MCP loaded?** Look for tools that can write files. If yes, the report can be saved automatically. If no, you will output the HTML inline and instruct the user to save it manually.

Tell the user what you found in plain English. If Monzo is not connected, offer to proceed with manual entry — the interview still works without it; the user just types their numbers.

## Procedure

### Phase 1 — Bank connection (skip if Monzo not available)

If the Monzo MCP is loaded:

1. Ask the user's permission before fetching anything: *"I can pull the last 90 days of your Monzo activity to make this faster — balance, pots, and categorised spend. Shall I?"*
2. On consent, call the Monzo tools to get:
   - Current balances across all accounts (personal, joint, flex, savings pots).
   - Last 90 days of transactions.
3. Aggregate the transactions into the categories defined in `reference/ons-benchmarks.md`. Do this with a single LLM pass — do not write code unless the user has a code execution tool loaded.
4. Summarise back to the user: *"I'm seeing roughly £X/month in housing-like costs, £Y in groceries, £Z in transport. Pots total £W. Does that match your sense of things?"*

If the user has **other** accounts (which most people do — joint mortgage account at another bank, salary going to a different bank, ISA at a third), explicitly ask for those numbers in Phase 2 rather than assuming Monzo represents the whole picture. Be candid: *"Monzo gives me a good window but probably not the full one — what else should I know about?"*

### Phase 2 — Interview

Conduct the five-stage interview. Read `reference/interview-script.md` for the full question set. Keep it conversational; don't fire questions at the user as a checklist.

The five stages, in order:

1. **Lifestyle context** — age, household, region, target retirement age, FIRE flavour (Lean/Standard/Fat).
2. **Income & savings rate** — net monthly income, pension contributions (employee + employer), other income.
3. **Net worth** — cash (use Monzo if connected), pensions, ISAs/GIAs, property equity, liabilities.
4. **Spending** — skip if bank-connected and aggregates already confirmed; otherwise ask for monthly take-home spend and top three categories.
5. **Risk & confirmation** — risk tolerance, then read back all inputs in plain English and get explicit assent before computing.

### Phase 3 — Compute the snapshot

Read `reference/fire-math.md` for the formulas. Compute the snapshot in your head (these are simple compound growth and division calculations — Claude is reliable at this). Emit the snapshot as a single JSON object matching `reference/output-schema.json`.

Verify before continuing:
- Does `monthlySurplus = monthlyNetIncome - monthlySpend`? If not, re-check inputs.
- Is the `fireNumber` ≥ £100k? Sanity check; if it's tiny, you've probably misread inputs.
- Are RAG flags consistent with the thresholds in `reference/fire-math.md`?

### Phase 4 — Render the report

Read `templates/report.html`. Substitute the JSON values into the template. Save it to disk as `fire-ready-report-YYYY-MM-DD.html` in the user's home directory (or wherever the filesystem MCP is rooted). Tell the user:

> "I've saved your report to `[path]`. Open it in any browser, then File → Print → Save as PDF for a one-page PDF."

If the filesystem MCP is not available, output the full HTML directly in chat inside a markdown code block and instruct the user to copy-paste it into a new file called `fire-ready-report.html`.

### Phase 5 — Walk them through it

After saving, give the user a 60-second verbal summary of the report. Lead with the trajectory RAG status (the headline finding), then the one most actionable recommendation. Do not dump all three recommendations on them; pick one and ask if they want to dig in.

End every report walkthrough with:
> "Reminder — this is information, not advice. Numbers will move as your inputs do; come back any time and we'll redo it."

## What this skill must NEVER do

- Recommend a specific fund, ETF, platform, broker, or pension provider by name.
- Generate a number for "how much you should be saving" without anchoring it to the user's stated target retirement age and FIRE flavour.
- Write or move money. The Monzo MCP at samaxbytez/monzo-mcp is read-only; do not attempt write operations even if the tools appear.
- Store the user's data anywhere outside the conversation. The PDF lives on their machine.
- Continue the interview if the user expresses financial distress (e.g. mentions struggling with debt, can't afford essentials). In that case, pause empathetically and signpost: *"It sounds like things are tight right now — before we go further, MoneyHelper.org.uk has free, regulated guidance for situations like this. Would you like to come back to FIRE planning another time?"*

## Reference files

Load these only when you need them — keeps your context clean.

- `reference/fire-math.md` — formulas, RAG thresholds, withdrawal rate options.
- `reference/ons-benchmarks.md` — UK Family Spending data by household type.
- `reference/interview-script.md` — full question script with conversational variants.
- `reference/output-schema.json` — JSON shape for the snapshot.
- `reference/guardrails.md` — extended refusal patterns and disclaimer language.
- `templates/report.html` — the report template (substitute placeholders).
- `examples/sample-snapshot.json` — a worked example for testing.

## Tagline & footer

The tagline appears in the report header: **"Everyone should be ready to retire."**

The disclaimer appears in the report footer:
> *Information, not regulated financial advice. Sources: UK ONS Family Spending [year]. Generated by FIRE Ready, an open-source Claude skill.*
