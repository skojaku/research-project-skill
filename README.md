# Research Project Skill

A skill that manages a research project's full lifecycle — from kicking off a new experiment, through running it and recording findings, to marking the results ready for the paper.

I run a lot of small, self-contained experiments. Each one deserves its own home, a clear statement of the question, and a way to track where it stands. Left to myself (or an AI assistant), the setup drifts: folders get named inconsistently, the "what was I even testing?" note never gets written, and half-finished threads vanish. This skill captures the routine I want followed every time, so the boring-but-important scaffolding happens automatically and I can stay focused on the science.

## What it does

Every experiment lives in its own dated folder under `exps/`, fronted by a `NOTE.md` that explains the question in plain language, and tracked by a GitHub issue whose label records the stage. The skill runs the whole arc:

1. **Start** — create `exps/YYYY-MM-DD-short-desc/`, load my [Snakemake conventions](https://github.com/skojaku/snakemake-skill), write a `NOTE.md` (the question, stated so a reader understands it without opening any code), and open a GitHub tracking issue labelled `experiment-needed`.
2. **Run and record** — keep the `NOTE.md` current as the work progresses, adding a findings section with the answer.
3. **Advance the stage** — move the issue's label as the work moves: `experiment-needed` → `ready-to-paper` → `documentation`, and close it when the thread is done.

It also encodes two habits I care about:

- **Keep the design simple.** Favour the simplest experiment that can answer the question. If you can't explain it in a couple of plain sentences, it's too complicated — cut it down before running anything.
- **Talk about results in plain language.** Lead with the answer, no invented acronyms, no code-speak (say what a thing *is*, not its variable name), and explain findings so they stand alone for someone who never opened the files.

## Installation

### Using `npx skills` (works across agents)

[`npx skills`](https://github.com/vercel-labs/skills) is a package manager for AI agent skills. It installs to Claude Code, Codex, Cursor, and others.

```bash
# Install to all detected agents
npx skills add skojaku/research-project-skill --all

# Or target a specific agent
npx skills add skojaku/research-project-skill -a claude-code
npx skills add skojaku/research-project-skill -a codex
```

### Manual install

**Claude Code:** Copy `skills/research-project/SKILL.md` to `~/.claude/skills/research-project/SKILL.md`

**Codex / Cursor / other agents:** Copy the contents of `skills/research-project/SKILL.md` into your `AGENTS.md` or system prompt.

## Adapting it to your project

The skill is written around my own setup, so a few things are worth changing for yours:

- **Folder root.** I keep experiments under `exps/`. Change the path if you use something else.
- **Issue labels.** It uses `experiment-needed`, `ready-to-paper`, and `documentation` to mark each stage. Rename them to match your tracker (the skill creates `ready-to-paper` if it's missing).
- **Snakemake.** Stage 1 loads my [Snakemake skill](https://github.com/skojaku/snakemake-skill) before writing any pipeline. Drop that step if you don't use Snakemake.
