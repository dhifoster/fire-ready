# FIRE Ready — a Claude Desktop skill

> Everyone should be ready to retire.

A Claude Desktop skill that gives you a **one-page PDF report on your retirement readiness** in about ten minutes. Optionally connects to your Monzo account so you don't have to type your spending in by hand.

It's an open-source MVP. Bring your own Claude Desktop, your own Monzo developer token, and your own numbers. Nothing leaves your machine.

---

## What you get

- A 5-stage interview run by Claude that asks the questions a financial planner would ask.
- An automatic spending breakdown from your Monzo transactions (last 90 days).
- A FIRE projection with a year-by-year trajectory chart.
- A RAG (Red / Amber / Green) goal tracker across savings rate, emergency fund, allocation, and trajectory.
- Up to three personalised recommendations for what to do next.
- A one-page HTML report saved to your machine that prints cleanly to PDF.

**This is information, not regulated financial advice.** The skill refuses to recommend specific products and signposts to FCA-authorised advisors when needed.

---

## Prerequisites

| What | Why | Where |
|---|---|---|
| [Claude Desktop](https://claude.ai/download) | Runs the skill | claude.ai/download |
| Node.js v18+ | For the Monzo MCP server | nodejs.org |
| A Monzo account (UK only) | Optional — skill works without it | monzo.com |
| A Monzo developer token | Read-only, you create it yourself | developers.monzo.com |

If you don't have Monzo, skip Steps 3 and 4 below. The skill prompts you to enter spending manually instead.

---

## Install

### 1. Clone this repo

```bash
git clone https://github.com/<your-username>/fire-ready-skill.git
cd fire-ready-skill
```

### 2. Place the skill where Claude Desktop can find it

Claude Desktop loads skills from a designated skills directory. Move (or symlink) the `fire-ready-skill` folder there. The exact path depends on your platform — check Claude Desktop's settings under **Skills** for the current location.

```bash
# Example (macOS — adjust to whatever Claude Desktop tells you)
mv fire-ready-skill ~/Library/Application\ Support/Claude/skills/
```

### 3. (Optional) Install the Monzo MCP server

This skill is built to work with [`samaxbytez/monzo-mcp`](https://github.com/samaxbytez/monzo-mcp), a community MCP server for Monzo. It's read-only — it cannot move money.

```bash
# Install globally so npx can find it
npm install -g monzo-mcp
```

### 4. (Optional) Get your Monzo developer token

1. Go to https://developers.monzo.com and sign in with your Monzo account.
2. Click **Get a developer access token** in the Playground.
3. Approve the access request in the Monzo app on your phone (you'll get a push notification).
4. Copy the access token — you'll need it in Step 5.

**Note:** Monzo developer tokens are short-lived (~hours). If your token expires, repeat this step. For longer-lived access you'd need to register an OAuth client; for an MVP, the playground token is fine.

### 5. Configure Claude Desktop

Open Claude Desktop's config file:

| OS | Path |
|---|---|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

Add (or merge) the following — see `setup/claude_desktop_config.example.json` in this repo for a copy-paste version:

```json
{
  "mcpServers": {
    "monzo": {
      "command": "npx",
      "args": ["monzo-mcp"],
      "env": {
        "MONZO_ACCESS_TOKEN": "paste-your-token-here"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/your-username/Documents/fire-ready-reports"
      ]
    }
  }
}
```

Two things to note:
- The `filesystem` server is what lets Claude save the HTML report to disk. Point it at any folder on your machine.
- If you skipped Steps 3–4, leave the `monzo` block out. The skill will still work; it'll ask for your numbers manually.

### 6. Restart Claude Desktop

Quit and reopen. You should see a "FIRE Ready" skill listed in the skills menu, and (if configured) a green dot next to "monzo" and "filesystem" in the MCP indicator.

---

## Usage

Open Claude Desktop and say something like:

> *"Run a FIRE check on me."*

or

> *"Am I on track for retirement? Use the FIRE Ready skill."*

Claude will:
1. Confirm what tools are available.
2. (If Monzo is connected) ask permission, fetch your last 90 days, and summarise.
3. Walk you through the five-stage interview.
4. Compute your snapshot and save the report.
5. Walk you through what it means and what to do next.

The whole thing takes ~10 minutes. You can stop at any point and resume later.

---

## What the report looks like

A single A4 page with five sections:

1. **Net worth & cashflow** — current totals, monthly surplus, savings rate.
2. **Spend vs UK benchmark** — your spending by category against ONS Family Spending data.
3. **Projection** — your net worth trajectory and the year you hit your FIRE number.
4. **Goal tracker** — RAG status across savings rate, emergency fund, allocation, and trajectory.
5. **Recommendations** — up to three actionable next steps.

See `examples/sample-report.pdf` for a worked example.

---

## Privacy & data

- **Nothing leaves your machine.** Your Monzo data is fetched by the local Monzo MCP server, processed by Claude Desktop, and saved to your local filesystem. Anthropic sees the conversation (because Claude is hosted), but your raw bank data is never sent anywhere except between your machine and Monzo's servers — and the report you generate is local-only.
- **The Monzo token you generate is read-only.** It cannot move money. <a href="https://github.com/samaxbytez/monzo-mcp">samaxbytez/monzo-mcp</a> exposes balance, transactions, and pots only.
- **You can revoke access at any time.** Log into the Monzo app, go to Privacy & Security → Connected apps, and remove your developer token.

---

## Troubleshooting

**"FIRE Ready skill isn't showing up"** — Check the path you put the skill folder in matches what Claude Desktop expects under Settings → Skills. Restart Claude Desktop after any change.

**"Monzo MCP isn't connecting"** — Run `npx monzo-mcp` directly in a terminal with `MONZO_ACCESS_TOKEN` set, see if it errors. Most often it's an expired token.

**"The report didn't save anywhere"** — The filesystem MCP needs to be configured with a writable folder. Check the path in your `claude_desktop_config.json`. If you can't get the filesystem MCP working, the skill will paste the HTML in chat — copy it into a new file called `fire-ready-report.html` and open in a browser.

**"My Monzo numbers don't match what I see in the app"** — The skill aggregates the last 90 days. If you've recently changed banks or just opened your Monzo, the picture will be partial. Tell Claude in the interview, and it will weight things accordingly.

---

## What this is not

- **Not regulated advice.** If you need it, find an FCA-authorised advisor at unbiased.co.uk or vouchedfor.co.uk.
- **Not a substitute for a real financial plan.** It's a snapshot, not a strategy.
- **Not a multi-bank aggregator.** It only reads Monzo. If you bank elsewhere, you tell Claude in the interview.
- **Not a budgeting tool.** It uses spend to project the future, not to nag you about lattes.

---

## Roadmap

- [ ] Starling MCP support (when one ships) — same shape, different tool calls.
- [ ] Aggregator-based MCP for non-Monzo users (Tink / Plaid / GoCardless).
- [ ] Couples mode — two interviews stitched into a joint snapshot.
- [ ] Scenario sliders — "what if I save £200 more / month?" with re-projection.
- [ ] Eval suite — 20 archetype profiles to assert the snapshot output is stable.

PRs welcome.

---

## Licence

MIT. See `LICENSE`.

## Acknowledgements

- [samaxbytez/monzo-mcp](https://github.com/samaxbytez/monzo-mcp) — the Monzo MCP server that makes this skill data-grounded.
- [Anthropic](https://anthropic.com) — for Claude and the Model Context Protocol.
- UK Office for National Statistics — for the Family Spending dataset that underpins the benchmarks.
