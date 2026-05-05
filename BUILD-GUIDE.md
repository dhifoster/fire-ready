# Build Guide — FIRE Ready with Claude Code

This guide walks you through building, testing, iterating on, and shipping the FIRE Ready skill set using Claude Code. It assumes you've got the PRD (`PRD.md`) and the four scaffolded skills already in this repo.

It's written for someone who's comfortable in a terminal but isn't necessarily a daily git/CLI user. Skip past anything that's already familiar.

---

## What you'll do

1. Install the prerequisites
2. Set up the local repo
3. Use Claude Code to refine the skills against the PRD
4. Test the agent end-to-end in Claude Desktop
5. Run manual evals against fixture personas
6. Push to GitHub

Total time, first pass: about 2–3 hours.

---

## 1. Prerequisites

| Tool | Why | Install |
|---|---|---|
| **Claude Desktop** | Runs the skill end-to-end, the way users will experience it | https://claude.ai/download |
| **Claude Code** | Builds and iterates on the skill files | https://claude.com/claude-code |
| **Git** | Version control + GitHub push | https://git-scm.com/downloads |
| **GitHub account** | Hosts the repo | https://github.com/signup |
| **GitHub CLI** *(optional but recommended)* | Easier remote setup than the web UI | https://cli.github.com/ |

You don't need Node.js, Python, or any other runtime for the MVP — the skills are markdown files with no scripts.

---

## 2. Local repo setup

If you're starting from this scaffold:

```bash
# Move the project into a working location of your choice
cd ~/projects   # or wherever you keep code

# If you cloned the scaffold from somewhere, you're already set.
# If you're starting fresh from the files, init a repo:
cd fire-ready
git init
git add .
git commit -m "Initial scaffold from PRD"
```

You should now have:

```
fire-ready/
├── README.md
├── PRD.md
├── BUILD-GUIDE.md
├── skills/
│   ├── fire-ready/SKILL.md
│   ├── spending-benchmarks/SKILL.md
│   ├── spending-benchmarks/references/uk-ons-benchmarks.md
│   ├── fire-calculations/SKILL.md
│   └── report-generator/SKILL.md
└── evals/
    └── (empty for now)
```

---

## 3. Refining the skills with Claude Code

Open Claude Code in the project directory:

```bash
cd fire-ready
claude
```

Below are prompts that move you through the build. Run them in order on your first pass; later, cherry-pick as needed.

### 3.1 — Pressure-test the scaffold against the PRD

The skills were scaffolded directly from the PRD, but a fresh pass with Claude Code reading both will surface gaps.

> **Prompt:**
> Read `PRD.md` and the four skill files under `skills/`. Identify any place where the skill files don't fully implement what the PRD specifies, or where the skills could be tightened. Don't change anything yet — give me a numbered list of issues, ranked by importance. Flag anything that's actually a PRD gap rather than a skill gap.

Read the list. Decide what's worth fixing now vs deferring.

### 3.2 — Fix the gaps

> **Prompt:**
> For issues 1–[N] from the previous list, update the relevant skill files. Make changes precise — don't rewrite sections that aren't affected. Show me a diff of each change before saving.

### 3.3 — Generate the eval fixtures

> **Prompt:**
> Based on the eval section of `PRD.md`, generate the five fixture personas as separate markdown files under `evals/personas/`. Each fixture should have:
> - A short narrative (who they are, what their financial situation is)
> - All the numbers needed to walk the interview end-to-end (income, spending by category, assets, debts, goals, current age, location)
> - The expected qualitative output characteristics (e.g., "high-interest debt flag should fire", "leak should be ~£600/month", "should be persona P1")
>
> Don't compute exact numbers — use realistic figures and let the test runs surface whether the calculations work.

### 3.4 — Walk a fixture through the interview

> **Prompt:**
> Using fixture persona `evals/personas/p1-mid-income-debt.md`, simulate playing the user's role through the full FIRE Ready interview. At each stage, paste what the user would say and what you (as the orchestrator skill) would respond. Don't actually invoke the skill — just walk the script. Flag any place where the flow feels rough, the questions don't make sense, or the orchestrator would have ambiguity about what to do.

This is the cheapest test you'll do. Most flow problems surface here.

### 3.5 — Tighten language

> **Prompt:**
> Read the orchestrator skill (`skills/fire-ready/SKILL.md`) with a critical eye for tone. Are there places the skill would come across as preachy, condescending, or financially prescriptive? Suggest specific edits to keep it warm but neutral. Don't change anything yet — show me the suggestions first.

### 3.6 — Regulatory sweep

> **Prompt:**
> Scan all four skill files for anything that could be interpreted as regulated financial advice under FCA rules — specific product recommendations, allocation guidance, buy/sell suggestions, or "you should" framing. List anything you find with a proposed rewording. Be strict. This is the single most important compliance check.

This eval is non-negotiable. If anything fails here, fix it before going further.

---

## 4. Local testing in Claude Desktop

Claude Code is great for editing skills. Claude Desktop is where you actually experience them.

### 4.1 — Find your Claude Desktop skills directory

The path varies by OS and may change between Claude Desktop versions. Check Claude Desktop's settings under **Skills** for the current location. Typical locations:

- **macOS:** `~/Library/Application Support/Claude/skills/`
- **Windows:** `%APPDATA%\Claude\skills\`
- **Linux:** `~/.config/Claude/skills/`

### 4.2 — Symlink your skills folder

Symlinking (rather than copying) means edits in your repo show up immediately in Claude Desktop after a restart.

**macOS / Linux:**
```bash
# From the project directory
ln -s "$(pwd)/skills/fire-ready"           ~/Library/Application\ Support/Claude/skills/fire-ready
ln -s "$(pwd)/skills/spending-benchmarks"  ~/Library/Application\ Support/Claude/skills/spending-benchmarks
ln -s "$(pwd)/skills/fire-calculations"    ~/Library/Application\ Support/Claude/skills/fire-calculations
ln -s "$(pwd)/skills/report-generator"     ~/Library/Application\ Support/Claude/skills/report-generator
```

**Windows (PowerShell, run as admin):**
```powershell
New-Item -ItemType SymbolicLink -Path "$env:APPDATA\Claude\skills\fire-ready" -Target "$pwd\skills\fire-ready"
# Repeat for the other three
```

### 4.3 — Restart Claude Desktop

Quit fully (not just close the window) and relaunch. The four skills should now appear in the skills menu.

### 4.4 — Run an interview

Open a new chat and try one of:

> *"Run a FIRE check on me."*
> *"I have no idea where my money goes. Can you help me figure out if I'm on track to retire?"*
> *"Am I saving enough?"*

The orchestrator should pick up the trigger and start the interview.

### 4.5 — Watch for triggering misses

If the skill doesn't trigger when you expect it to, that's a description problem, not a behaviour problem. Open the orchestrator's `SKILL.md` and tighten the description — make it more specific about the situations it should fire in.

---

## 5. Manual eval runs

For each of the five fixture personas:

1. Open a fresh Claude Desktop chat.
2. Use the prompt: *"Run a FIRE check on me. I'll act out a persona."*
3. Paste the persona narrative.
4. Walk through the interview, answering as the persona would.
5. Capture the final snapshot output.
6. Score against the eval checklist from the PRD (six categories).
7. Log results in `evals/results/iteration-N.md`.

The regulatory checklist (category 5 in the PRD) is the blocker. Any single failure is a stop-the-line moment.

> **Suggested log format** (`evals/results/iteration-1.md`):
> ```markdown
> # Eval iteration 1 — [date]
>
> ## Fixture: p1-mid-income-debt
> - Conversation flow: ✅ (7m20s)
> - Calculation accuracy: ✅
> - Persona adaptation: ✅ (P1 detected; explainer included)
> - Edge case (high-interest debt): ✅ (caveat appeared correctly)
> - Regulatory: ✅ (no specific products mentioned; FCA signpost offered)
> - Tone: ⚠️ (slightly preachy when discussing debt — see line "[…]")
>
> ## Fixture: p2-high-saver
> ...
> ```

After running all five, decide: ship as-is, or one more iteration? Use Claude Code to make targeted fixes from the log.

---

## 6. Pushing to GitHub

### 6.1 — Create the repo

**Using GitHub CLI** (one-liner):
```bash
gh repo create fire-ready --public --source=. --remote=origin --push
```

**Using the web UI:**
1. Go to https://github.com/new
2. Name it `fire-ready`. Public or private — your call.
3. Don't initialise with a README (you already have one).
4. Then locally:
   ```bash
   git remote add origin https://github.com/<your-username>/fire-ready.git
   git branch -M main
   git push -u origin main
   ```

### 6.2 — Suggested ongoing workflow

- Work on a branch per change: `git checkout -b tighten-orchestrator-tone`
- Commit small, descriptive units: `git commit -m "Soften high-interest debt action wording"`
- Push and merge via PRs, even solo — it gives you a clean history.
- Tag versions when you ship a meaningful update: `git tag v0.1.0 && git push --tags`

### 6.3 — What goes in the repo

- ✅ All four skill folders
- ✅ PRD, README, build guide
- ✅ Fixture personas (`evals/personas/`)
- ✅ Eval results log (`evals/results/`)
- ❌ Anything containing your personal financial data — keep that local
- ❌ Claude Desktop symlinks — they're machine-specific

A `.gitignore` worth starting with:

```
.DS_Store
*.log
/evals/personal/
.env
```

---

## 7. Iteration tips

**Update the PRD first, then the skills.** The PRD is the source of truth. If you find yourself making a behaviour change that contradicts the PRD, update the PRD in the same commit so the spec doesn't drift.

**Eval after every meaningful change.** Even one fixture run is enough to catch regressions. The full five takes about an hour.

**Resist scope creep.** The roadmap section of the PRD exists so you can park ideas without building them. If you find yourself writing a feature the PRD doesn't list, ask whether it earns its place in MVP scope or belongs on the roadmap.

**The orchestrator description is the most-tweaked file.** Trigger accuracy depends on the description being specific about *when* to fire. Plan to iterate on it 3–5 times before it's right.

**When you're ready for v0.2:** the obvious next moves are saveable file output (HTML/PDF report) and bank integration via MCP. The PRD's roadmap section ranks them.

---

## Useful Claude Code prompts to keep around

A handful of prompts that are worth saving in a notes file for repeat use:

> *"Read the PRD and the four skills. Tell me what's drifted between them since the last commit."*

> *"Run a fixture walk on `evals/personas/[name].md` and produce the eval result entry for `evals/results/iteration-[N].md`."*

> *"Review the orchestrator's description field. Suggest 3 alternative phrasings, each emphasising a different trigger scenario, ranked by which you think would have the best trigger accuracy."*

> *"Audit the entire skill set for FCA regulatory line crossings. Flag anything that could be interpreted as financial advice. Be strict."*

> *"I want to add [new feature]. Update the PRD first, then propose the skill changes needed. Don't write code yet."*

---

That's the loop. Build, test, eval, iterate, ship. The PRD keeps you honest about scope; the evals keep you honest about quality; Claude Code does the typing.
