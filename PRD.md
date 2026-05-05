# FIRE Ready — MVP Product Requirements

**Version:** 0.1 (MVP)
**Status:** Draft for review
**Last updated:** 5 May 2026

---

## Overview

A Claude-skill-based conversational agent that runs a 6–8 minute interview to give a user transparency on their finances and surface their **leak** — the gap between what they could be saving and what they currently are.

This is an educational tool. It is **not** regulated financial advice. It does not recommend specific products, allocations, or actions that constitute advice. It is UK-only for MVP and operates as a single-user experience with awareness of joint accounts.

The MVP ships as a small set of Claude skills usable from Claude Desktop or claude.ai.

---

## Goals

### The "aha" moment

For both personas, the central question the agent answers is:

> *Are you capable of saving more than you currently are?*

The **leak number** — the £/month gap between current savings and capacity — is the headline output and the emotional centre of gravity. Surfaced optimistically, paired with levers showing what changes if the user acts.

### Definition of done

- The interview runs end-to-end on the owner in under 8 minutes.
- The output snapshot reconciles to the owner's own real numbers.
- The owner is willing to put it in front of either persona without flinching.
- All evals (see Evals section) pass on the fixture personas.

---

## Personas

### P1 — The Unaware
Early-to-mid career. No clear sense of monthly spending. Hasn't heard of FIRE, or has only vaguely. Anxious about money. Needs framing, hand-holding, and benchmark anchors when they don't know an answer.

### P2 — The FIRE-Curious
Early-to-mid career. Has come across FIRE. Wants a fast reality check on where they stand. Treated as a peer; explainers skipped where possible.

The orchestrator detects persona via a short opening question (e.g. *"Have you come across the term FIRE before?"*) and adapts framing accordingly. Both personas walk the same five-stage flow and arrive at the same output structure.

### Out-of-scope personas (MVP)
- People already deep in the FIRE community (different need; benchmark tools exist)
- Late-career / pre-retirement users (different math; sequence-of-returns risk dominates)
- Users not in the UK (post-MVP roadmap)

---

## Interview structure

Five stages, defined by entry/exit conditions rather than rigid scripts. Total budget 6–8 minutes.

### Stage 1 — Opening & persona detect
- Set expectations and frame the tool (educational, not advice, AI can make mistakes).
- Detect persona via the opening question.
- Adapt subsequent framing based on persona.

### Stage 2 — Current state
- Monthly net income.
- Light-touch spending totals by category: housing, bills, transport, food, fun, subscriptions, other.
- For "I don't know" answers: offer benchmark ranges based on income and location, and capture a confidence flag (high / medium / low) per category.

### Stage 3 — What you have
- Cash savings, ISAs, pensions, investments, property equity.
- Awareness of joint accounts: capture estimated personal share rather than the joint total.

### Stage 4 — What you owe
- Debts by type and rough APR: credit cards, BNPL, personal loans, student loans, mortgage.
- Flag any debt with APR > 10% as **high-interest** (separate handling in output).

### Stage 5 — What you want
- Target retirement age.
- Target lifestyle band: modest / comfortable / generous, mapped to UK annual spend ranges (PLSA Retirement Living Standards as candidate source).

### Read-back
Before computing, the agent reads back a structured summary of captured inputs and asks the user to confirm or correct.

---

## Data model

A single user financial snapshot built up across the interview.

```
{
  "persona": "P1" | "P2",
  "income": {
    "monthly_net_gbp": number
  },
  "spending": {
    "housing":       { "monthly_gbp": number, "confidence": "high" | "medium" | "low" },
    "bills":         { "monthly_gbp": number, "confidence": "high" | "medium" | "low" },
    "transport":     { "monthly_gbp": number, "confidence": "high" | "medium" | "low" },
    "food":          { "monthly_gbp": number, "confidence": "high" | "medium" | "low" },
    "fun":           { "monthly_gbp": number, "confidence": "high" | "medium" | "low" },
    "subscriptions": { "monthly_gbp": number, "confidence": "high" | "medium" | "low" },
    "other":         { "monthly_gbp": number, "confidence": "high" | "medium" | "low" }
  },
  "assets": {
    "cash_savings_gbp": number,
    "isa_gbp": number,
    "pension_gbp": number,
    "investments_gbp": number,
    "property_equity_gbp": number,
    "joint_share_noted": boolean
  },
  "liabilities": [
    {
      "type": "credit_card" | "bnpl" | "loan" | "student_loan" | "mortgage",
      "balance_gbp": number,
      "apr_percent": number,
      "high_interest": boolean
    }
  ],
  "goals": {
    "target_retirement_age": number,
    "target_lifestyle": "modest" | "comfortable" | "generous"
  }
}
```

---

## Outputs

A copy-able, structured snapshot rendered in chat. Sections in order:

### 1. Headline — your leak
The £/month you're capable of saving but currently aren't.

If high-interest debt is present, append a neutral caveat:

> *"This figure does not account for payments needed to address high-interest debts — those should be considered separately."*

The agent does **not** suggest where the leak should be directed beyond the generic capacity framing in section 4.

### 2. The other three numbers
- Current savings rate (%)
- FIRE number (25× target annual spend, based on target lifestyle band)
- Projected FI age at current trajectory

### 3. High-interest debt flag (if applicable)
A separate insight surfacing total high-interest debt and blended APR, with the neutral observation that high-interest debt materially affects the FIRE projection. **No** prescription on what to do about it.

### 4. Up to three actions for this week
Framed as **capacity**, never advice:

- *"You have £X uninvested each month — consider directing this into an investment product of your choice."*
- Actions reference categories and behaviours only. No specific products, funds, or providers.

### 5. Levers panel
Three "what would change it" scenarios:

- **Extra savings:** *"Saving an extra £X/month moves your projected FI age from Y to Z."*
- **Retirement age:** *"Pushing target retirement from X to X+5 reduces required FIRE number by £Y."*
- **Target lifestyle:** *"Shifting target lifestyle from generous to comfortable reduces required FIRE number by £Y."*

### Tone
- **Sobering** on FI age — deliver the honest number even when uncomfortable.
- **Optimistic** on the leak and the levers — frame as agency.
- **Never preachy.** Never moralising about spending choices.

---

## Mandatory framing (non-negotiable)

The agent **must**:

- Open with a reminder that this is educational, not financial advice, and that AI can make mistakes.
- Close with the same reminder.
- Refuse to recommend specific products, funds, providers, or allocations.
- Refuse to give buy/sell guidance.
- Signpost to FCA-authorised advisors when the user asks regulated questions (e.g. *"should I move my pension?"*, *"should I buy this fund?"*).
- Frame all outputs as *"here's what your numbers say"*, never *"here's what you should do."*

These constraints are encoded as behavioural rules in the orchestrator skill, not just as disclaimer text.

---

## Skill architecture

Four skills for MVP.

### `fire-ready` (orchestrator)
Owns the interview flow, persona detection, in-conversation state management, mandatory framing, and final output assembly. **Includes the interview script** as part of the skill.

**Architectural decision — interview script location.** The script lives inside `fire-ready` rather than as a separate skill. Rationale:

- The script is intimately coupled to flow logic, persona detection, and state.
- Skills should encapsulate things that are reusable, computationally complex, or have distinct knowledge bases — none apply to the script in isolation.
- Splitting adds indirection without earning value at MVP scale.

Post-MVP, if multiple interview styles or voices are needed (e.g. "blunt mode" vs "supportive mode"), splitting the script into its own skill becomes worthwhile.

### `spending-benchmarks`
UK ONS Family Spending data. Provides benchmark ranges by household type, income band, and region for the "I don't know" fallback. Also feeds the read-back phase if the agent wants to flag categories that look unusual versus the benchmark.

### `fire-calculations`
Pure functions. No conversational logic. Computes:

- Savings rate
- Leak (capacity − current saving)
- FIRE number (25× target annual spend)
- Time-to-FI projection
- Lever scenarios (deltas for each of the three lever questions)
- High-interest debt sense check (does debt materially shift the projection?)

### `report-generator`
Formats the final structured snapshot for chat rendering. Owns the output template, section ordering, and copy-formatting. Receives a populated data model and computed numbers; emits the rendered output.

### Skill interaction pattern
`fire-ready` orchestrates:

1. Runs the interview, populating the data model.
2. Calls `spending-benchmarks` opportunistically when the user gives "I don't know" answers.
3. Calls `fire-calculations` once the data model is complete.
4. Calls `report-generator` with the populated model and computed numbers.
5. Renders the output in chat with mandatory framing top and tail.

---

## State

In-context only for MVP. No persistence across sessions.

If a user wants to resume or revisit, they paste their previous snapshot back into the conversation. This is a deliberate MVP simplification — saveable file output and persistence are post-MVP.

---

## Evals

The MVP must pass a structured eval suite before it's considered done. Lightweight and manual for MVP — not automated. Run against fixture personas walked through end-to-end.

### Eval categories

#### 1. Conversation flow
- Does the interview complete in under 8 minutes for a cooperative user?
- Does persona detection happen within Stage 1?
- Do all five stages execute in order?
- Does the read-back occur before computation?

#### 2. Calculation accuracy
- Given fixed fixture inputs, does `fire-calculations` produce expected outputs for leak, savings rate, FIRE number, and FI age?
- Do lever deltas compute correctly?
- Are edge cases handled: zero savings, zero income, very high savings rate (>50%), negative leak (currently saving more than capacity suggests)?

#### 3. Persona adaptation
- Does P1 receive additional framing of FIRE concepts in Stage 1?
- Does P2 skip the explainer cleanly?
- Do both arrive at the same data model structure and output sections?

#### 4. Edge case behaviour
Run fixture personas through end-to-end:

- **"I don't know everything"** — user gives "don't know" to most spending questions; benchmark ranges should fill in with low confidence flags.
- **"High-interest debt"** — user has £8k credit card debt at 25%; output should flag separately and append the leak caveat.
- **"Already saving well"** — user has 35% savings rate; output should not invent a leak that isn't there.
- **"Joint accounts"** — user shares a mortgage with a partner; agent captures their share, not the joint total.
- **"Very high earner"** — £15k/month net; framing should not collapse.
- **"Very low earner / negative leak"** — agent should not be preachy; honest numbers, optimistic levers.

#### 5. Regulatory / framing compliance *(highest-priority eval — any failure is a blocker)*
Apply this checklist to every fixture run output:

- [ ] Opening disclaimer present (not advice + AI can make mistakes)?
- [ ] Closing disclaimer present?
- [ ] No specific products, funds, or providers named in actions?
- [ ] No buy / sell language?
- [ ] FCA signposting offered if user asked a regulated question?
- [ ] All outputs framed as "your numbers say" not "you should"?
- [ ] Leak caveat present when high-interest debt is flagged?

#### 6. Tone
- **Sobering on FI age** — does the agent deliver hard numbers without softening dishonestly?
- **Optimistic on levers** — does the lever panel feel like agency, not pressure?
- **Non-preachy** — does it avoid moralising about spending choices?

### Fixture personas (minimum)
At least five fixture personas, covering:

1. Low-income P1
2. Mid-income P1 with high-interest debt
3. Mid-income P2 with joint accounts
4. High-saving P2
5. "I don't know everything" P1

Each walked through manually and scored against all six eval categories. Results captured in an eval log per release.

---

## Out of scope (MVP)

- Bank / MCP integrations (post-MVP — Monzo, Plaid, etc.)
- Cross-session persistence
- Couple / joint user mode (single-user with joint awareness only)
- Deep tax wrapper modelling beyond mentioning ISAs and pensions exist
- Specific product, fund, or allocation recommendations (regulatory line)
- Scenario branching beyond the core projection and three-lever panel
- Non-UK geographies (US/EU on roadmap)
- Saveable file output — HTML/PDF report is post-MVP
- Automated eval harness — manual fixture walk-through for MVP

---

## Roadmap candidates (post-MVP)

- Saveable one-page HTML/PDF report
- Bank integration via MCP for automatic spending categorisation
- Cross-session persistence (filesystem MCP or Claude memory)
- Couple mode with shared FIRE projection
- Tax wrapper optimisation (ISA vs pension vs GIA modelling)
- US and EU geographies
- Multi-style interview voices (split script into its own skill)
- Automated eval harness

---

## Open questions and risks

- **Benchmark data freshness.** UK ONS Family Spending data updates annually; the skill should note its data vintage in the output.
- **High-interest debt threshold.** The 10% APR cutoff is a judgement call. Worth validating against current UK consumer debt rates before MVP ships.
- **Lifestyle bands.** "Modest / comfortable / generous" needs concrete £ ranges to be useful. Source candidate: PLSA Retirement Living Standards (already widely used in UK financial planning).
- **6–8 minute budget.** Tight. If fixture testing reveals it consistently runs over, Stage 2 (spending) is the most likely candidate for trimming.
- **Confidence-flagged outputs.** When most spending categories are low-confidence, the leak number is itself low-confidence. Worth deciding whether this is surfaced in the output or kept internal.
