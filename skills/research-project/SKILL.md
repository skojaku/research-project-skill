---
name: research-project
description: >-
  Manage a computational research project across its whole lifecycle in this repo
  — starting a fresh project, exploring new ideas, and promoting validated results
  into the reproducible master workflow. Use this whenever the user starts a new
  project, kicks off a new experiment / study / investigation ("let's test
  whether...", "new idea to look at...", "spin up a study on..."), records what an
  experiment found, or wants results moved into the paper's master workflow. It
  owns the project template, each experiment's dated folder under exps/, its
  NOTE.md lab notes, its GitHub tracking issue, and the master workflow. Trigger
  it even if the user doesn't say "experiment" but is clearly starting or
  advancing a piece of research. Do NOT trigger for narrow edits inside an
  already-scaffolded experiment — a Snakemake rule, a NOTE.md typo, a script
  tweak, a conda env, paper text, or a gh lookup.
---

# Research project lifecycle

Guiding principles, adapted from the [Zen of Python](https://peps.python.org/pep-0020/) —
they apply to experiment design and everything you build:

> Beautiful > ugly. Explicit > implicit. Simple > complex. Complex > complicated.
> Flat > nested. Readability counts. Special cases aren't special enough to break
> the rules, although practicality beats purity. In the face of ambiguity, refuse
> the temptation to guess. If an idea is hard to explain it's bad; if easy to
> explain it may be good.

Sharpest tests: if you can't explain an experiment in a couple of plain sentences,
it's too complicated; and in the face of ambiguity, ask — don't guess. A project
moves through three stages — match the stage to the user's intent.

## Fresh start

Starting from scratch — clone the template, strip its git history if it's a new
repo, adapt README and structure:

```bash
git clone https://github.com/skojaku/project-template/ <project-dir>
```

## Exploration

Exploring a new idea — create a self-contained workspace named by date and topic.
Use `date +%F`, don't guess it; `<experiment name>` is a short kebab handle (e.g.
`chunk-tda`). Layout mirrors the project's Snakemake conventions:

```
exps/<yyyy-mm-dd>-<experiment name>/
├── NOTE.md            # lab notebook (from assets/NOTE.template.md)
├── Snakefile          # config, globals, params, includes
├── workflow/
│   ├── rules/         # one .smk per pipeline stage
│   └── <category>/    # scripts by purpose: stats/, plot/, fitting/
└── data/              # outputs, tiered: preprocessed/, derived/, plot_data/
```

- **Snakemake** organizes the reproducible pipeline. Invoke the `snakemake` skill
  before writing rules — it owns the naming/wiring (UPPER_CASE `Path` constants,
  list-valued params via `to_paramspace()`/`.wildcard_pattern`, dual-mode scripts,
  one aggregation rule per stage). Don't reinvent those here.
- **Code and data live apart:** what you write goes under `workflow/`, what the
  pipeline produces under `data/`. Never commit large data — it regenerates.
- **NOTE.md** is the lab notebook, written as you go — the front page a reader
  understands without opening code. Start from `assets/NOTE.template.md`: a running
  log of dated entries (newest at the bottom, separated by `---`), each recording
  what you tried, the findings, the learning, and any gotcha. Append a new dated
  block per run; don't overwrite the last.
- **GitHub issue** tracks the experiment and reports findings/learnings. Open one
  at kickoff (title = experiment, body = question + folder path), keep it updated,
  label it `experiment-needed` while it still needs computation.

## Code preferences

- **Python** by default.
- **Environments:** `uv` for pip-shaped Python deps, `mamba` for environments and
  system-level / non-Python toolchains (conda-forge).
- **Scripts under ~200 lines** where you can — past that it's doing too much; split
  it or lift shared logic out.
- **`libs/`** at the project root holds reusable code (loaders, metrics, model
  wrappers) imported across experiments — imported, not copy-pasted. One-off
  pipeline steps stay in their experiment's `workflow/`.
- **`.env`** (gitignored) holds API keys/tokens — never hardcode them — and the
  machine's CPU/GPU/memory for reference, so results read against the hardware.

## Master workflow

When an exploration's results hold up and belong in the paper, promote them into
the **reproducible master workflow** — the final deliverable that reproduces the
paper end to end. It uses the same Snakemake layout but lives at the project root
(`Snakefile`, `workflow/`, `data/`), not under `exps/`. Promoting = lift the
validated rules and scripts out of the exploration folder into that root pipeline
and wire them into `rule all`, so the whole paper reproduces from one `snakemake`
run. Mark the issue `ready-to-paper` (create the label once if missing:
`gh label create ready-to-paper --description "Results ready to present in the paper" --color 0e8a16`);
use `documentation` for issues whose task is writeups, not experiments.

## Talking about results

The user reads conclusions, not code. Reporting a finding: **be brief** (lead with
the answer); **no invented acronyms**; **no code-speak** (say what a thing *is*, not
its variable/function name); **standalone** (sensible to someone who never opened the files).
