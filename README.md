# FIRE Ready

A Claude-skill-based conversational agent that runs a 6–8 minute interview to give a UK user transparency on their finances and surface their **savings leak** — the gap between what they could save and what they currently are.

Educational tool. UK-only. Single user, with awareness of joint accounts. **Not regulated financial advice.**

Note from the creator: I've purposefully added the vibed-PRD and Build Guide. I want to be transparent to other product managers on my workflow whilst we're all building our product management AI playbooks so other people can learn. Questions and feedback always welcome. 

---

## Status

MVP — v0.1. See [`PRD.md`](./PRD.md) for the full specification and [`BUILD-GUIDE.md`](./BUILD-GUIDE.md) for how to build, test, and ship it.

## What you get

- A 6–8 minute interview run by Claude that asks the questions a financial planner would ask, but framed for someone who genuinely doesn't know what they spend.
- A copy-able, structured snapshot in chat covering:
  - Your **leak** (the headline)
  - Current savings rate
  - FIRE number (25× target annual spend)
  - Projected FI age at current trajectory
  - High-interest debt flag (if applicable)
  - Three actions you could take this week
  - A "what would change the picture" lever panel
- Built-in regulatory guardrails — never recommends specific products, funds, or providers. Signposts to FCA-authorised advisors when appropriate.

## Architecture

Four Claude skills:

| Skill | Role |
|---|---|
| `fire-ready` | Orchestrator. Owns the interview, persona detection, framing, and final assembly. |
| `spending-benchmarks` | UK ONS-style spending ranges by category, income band, and region. Used as the fallback when the user gives "I don't know" answers. |
| `fire-calculations` | Pure functions. Computes savings rate, leak, FIRE number, time-to-FI, and lever scenarios. |
| `report-generator` | Owns the output template, action selection, and tone rules. |

```
skills/
├── fire-ready/
│   └── SKILL.md
├── spending-benchmarks/
│   ├── SKILL.md
│   └── references/
│       └── uk-ons-benchmarks.md
├── fire-calculations/
│   └── SKILL.md
└── report-generator/
    └── SKILL.md
```

## Requirements

Claude Desktop (Mac or Windows) — download at claude.ai/download
A terminal application (Terminal on Mac, Command Prompt on Windows)

## Quick start

See [`BUILD-GUIDE.md`](./BUILD-GUIDE.md) for the full walkthrough. Short version:

1. Clone this repo.
2. Copy the contents of `skills/` into your Claude Desktop skills directory (path varies by OS — check Claude Desktop's settings).
3. Restart Claude Desktop.
4. Open a new chat and the agent will run through your first conversation. Prompt:  *"Run a FIRE check on me."* or ask something along the lines of: *I have no idea where my money goes. Can you help me figure out if I'm on track to retire? Am I saving enough?* 

## What's not in MVP

- Bank / MCP integrations (Monzo, Plaid)
- Cross-session persistence
- Couple / joint user mode
- Saveable HTML/PDF report
- Non-UK geographies
- Automated eval harness

See [`PRD.md`](./PRD.md) for the full out-of-scope list and roadmap candidates.

## Privacy & Security

Your transaction data you share stays within your Claude Project and is subject to Anthropic's standard privacy policy. Version MVP 0.1 does not ask for any financial or banking credentials and future versions of this product will be stored in a local .env file on your own computer, unless explicity stated.

## Documents

- [`PRD.md`](./PRD.md) — Product requirements
- [`BUILD-GUIDE.md`](./BUILD-GUIDE.md) — How to build, test, and ship with Claude Code
- [`evals/`](./evals/) — Fixture personas for manual eval runs *(to be added)*

## Disclaimer

This is an educational tool. It is **not** regulated financial advice. It does not recommend specific products, funds, providers, or allocations. For regulated advice, consult an [FCA-authorised advisor](https://register.fca.org.uk/). Always verify tax figures and other thresholds with official government sources before acting on them.

The skill is provided as-is. Use at your own risk.

## License

MIT
