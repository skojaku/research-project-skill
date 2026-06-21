# Research Project Skill

This is an AI skill that manages computational research project. The key principles, which are an adaptation of [Zen of Python](https://peps.python.org/pep-0020/) by Tim Peters:

- Beautiful is better than ugly.
- Explicit is better than implicit.
- Simple is better than complex.
- Complex is better than complicated.
- Flat is better than nested.
- Readability counts.
- Special cases aren't special enough to break the rules.
- Although practicality beats purity.
- In the face of ambiguity, refuse the temptation to guess.
- If the idea is hard to explain, it's a bad idea.
- If the idea is easy to explain, it may be a good idea.

The skill has the following rules for different lifestage of a research project.

- **Fresh start**: When fresh start a project, [Downloads my project template](https://github.com/skojaku/project-template/) to get started.

- **Exploration**: When exploring a new idea, create a folder under `exps/<yyyy-mm-dd>-<experiment name>` as a workspace. Each exploration folder is self-contained — its own pipeline, data, and notes — so it can be reproduced, deleted, or revived without disturbing the rest. In the workspace,
  - [Snakemake](https://github.com/snakemake/snakemake) will be used to organize the reproducible pipeline (see my [Snakemake skill](https://github.com/skojaku/snakemake-skill) for the conventions). Code lives under `workflow/`; everything the pipeline produces lives under `data/` and is never committed — it regenerates.
  - It takes Lab note as it goes in NOTE.md. The note is a running log of dated entries separated by `---`, one per experiment in the exploration, each recording what I **Tried**, the **Findings**, the **Learning**, and any **Gotcha**. A template ships with the skill at `skills/research-project/assets/NOTE.template.md`.
  - It also reports the findings and learning in Github Issue.

- **Master workflow**: When the exploration pipeline generates results, hammered on well, and we want to include them in the paper, we include them into the reproducible master workflow under the project folder. It uses the same Snakemake layout but lives at the project root rather than under `exps/`. This master workflow is the final deliverable for reproducing the results.

## Code preferences

The skill also encodes a few coding habits:

- **Python** by default. `uv` for pip-shaped dependencies, `mamba` for environments and system-level / non-Python toolchains.
- **Short scripts** — under ~200 lines where possible. Past that, a script is doing too much; split it or lift the shared logic out.
- **`libs/`** at the project root holds reusable code (loaders, metrics, model wrappers) imported across experiments. One-off pipeline steps stay in their experiment's `workflow/`.
- **`.env`** (gitignored) holds API keys and tokens — never hardcoded — and the machine's CPU / GPU / memory for reference, so results can be read against the hardware that produced them.

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
