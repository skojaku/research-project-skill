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

This skill manages a computational research project. Its guiding principles are an
adaptation of the [Zen of Python](https://peps.python.org/pep-0020/) — they apply
to experiment design and to everything you build here:

> - Beautiful is better than ugly.
> - Explicit is better than implicit.
> - Simple is better than complex.
> - Complex is better than complicated.
> - Flat is better than nested.
> - Readability counts.
> - Special cases aren't special enough to break the rules.
> - Although practicality beats purity.
> - In the face of ambiguity, refuse the temptation to guess.
> - If the idea is hard to explain, it's a bad idea.
> - If the idea is easy to explain, it may be a good idea.

The last two are the sharpest test: if you can't explain an experiment to the
user in a couple of plain sentences, it's too complicated — cut it down before
running anything. And in the face of ambiguity, don't guess — ask.

A project moves through three stages. Match the stage to what the user is doing.

## Fresh start

When starting a project from scratch, download the project template to get going:

```bash
git clone https://github.com/skojaku/project-template/ <project-dir>
```

Then strip the template's git history if it's becoming a new repo, and adapt the
README and structure to the project at hand.

## Exploration

When exploring a new idea, create a workspace folder named by date and topic:

```
exps/<yyyy-mm-dd>-<experiment name>/
```

Use `date +%F` for the date — don't guess it. `<experiment name>` is a short
kebab-case handle for the question (e.g. `chunk-tda`). Inside the workspace:

- **Snakemake** organizes the reproducible pipeline. Invoke the `snakemake` skill
  before writing any rules so the pipeline matches house conventions.
- **NOTE.md** is the lab notebook — write it as you go. It is the front page of
  the experiment: a reader should understand the question and what you found
  without opening any code. Start it from `assets/NOTE.template.md`. The template
  is a running log: a title with the one-line takeaway, then one dated entry per
  experiment within this exploration, newest at the bottom, separated by `---`.
  Each entry records what you tried, the findings, the learning, and any gotcha.
  One exploration folder holds many such entries as the idea develops — append a
  new dated block each time you run something, don't overwrite the last.
- **GitHub issue** tracks the experiment and is where you report findings and
  learnings. Open one at kickoff (title = the experiment, body = the question +
  folder path) and keep it updated as the work teaches you things. Label it
  `experiment-needed` while it still needs computation.

## Organizing files

Each exploration folder is a self-contained mini-project — it has its own pipeline,
its own data, its own notes, and nothing reaches across into a sibling. That
isolation is what lets you reproduce, delete, or revive an experiment without
disturbing the rest. The layout mirrors the project's Snakemake conventions:

```
exps/<yyyy-mm-dd>-<experiment name>/
├── NOTE.md            # lab notebook (from assets/NOTE.template.md)
├── Snakefile          # config, globals, params, includes
├── workflow/
│   ├── rules/         # one .smk per pipeline stage
│   └── <category>/    # scripts grouped by purpose: stats/, plot/, fitting/
└── data/              # outputs, tiered: preprocessed/, derived/, plot_data/
```

Two rules carry most of the weight, so make them explicit:

- **Code and data live apart.** Everything you write goes under `workflow/`;
  everything the pipeline produces goes under `data/`. Never commit large data —
  the pipeline regenerates it. The point is that the code is the artifact and the
  data is disposable.
- **Defer the pipeline details to the `snakemake` skill.** It owns the naming and
  wiring (UPPER_CASE `Path` constants, list-valued params via `to_paramspace()` /
  `.wildcard_pattern`, dual-mode scripts that run inside *and* outside Snakemake,
  one aggregation rule per stage). Load it before writing rules rather than
  reinventing those conventions here.

## Master workflow

When an exploration's pipeline has produced results, been hammered on until they
hold up, and you want them in the paper, promote them into the **reproducible
master workflow** under the project folder. This master workflow is the final
deliverable — the thing that reproduces the paper's results end to end. Moving an
experiment here means it has graduated from scratch work to a result you stand
behind; mark its issue `ready-to-paper` (create the label once if missing:
`gh label create ready-to-paper --description "Results ready to present in the paper" --color 0e8a16`).
Use `documentation` for issues whose task is writeups rather than experiments.

The master workflow uses the same Snakemake layout as an exploration folder, but
lives at the project root (`Snakefile`, `workflow/`, `data/`) rather than under
`exps/`. Promoting means lifting the validated rules and scripts out of the
exploration folder into that root pipeline and wiring them into `rule all`, so the
whole paper reproduces from one `snakemake` invocation.

## Talking about results

The user reads conclusions, not code. When reporting a finding:

- **Brief.** Lead with the answer; a few sentences beat a wall of text.
- **No invented acronyms.** Use plain words, not shorthand the user must decode.
- **No code-speak.** Say what a thing *is*, never its variable/function/column name.
- **Standalone.** Explain so it makes sense to someone who never opened the files.
