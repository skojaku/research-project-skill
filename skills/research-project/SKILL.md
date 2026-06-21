---
name: research-project
description: >-
  Manage a research project's full lifecycle in this repo — from kicking off a
  new experiment, through running it and recording findings, to marking results
  ready for the paper. Use this whenever the user starts a new experiment, study,
  or investigation ("let's test whether...", "new idea to look at...", "spin up a
  study on..."), AND whenever they move an existing experiment to its next stage:
  results are in, it's ready to write up, or it needs documentation. It owns the
  experiment folder under exps/, the NOTE.md writeup, and the GitHub tracking
  issue + its stage label. Trigger it even if the user doesn't say "experiment"
  but is clearly starting or advancing a piece of research. Do NOT trigger for
  narrow edits inside an already-scaffolded experiment — a Snakemake rule, a
  NOTE.md typo, a script tweak, a conda env, paper text, or a gh lookup.
---

# Research project lifecycle

Every experiment lives in its own dated folder under `exps/`, fronted by a
`NOTE.md` and tracked by a GitHub issue. This skill runs the whole arc so each
thread stays findable and the user has one place to follow it. Match the house
style of existing siblings (e.g. `exps/2026-06-06-pwc-method-similarity/`).

## Stage 1 — Start

1. **Folder:** `exps/$(date +%F)-short-desc/`, where `short-desc` is a 2–4 word
   kebab handle (e.g. `chunk-tda`). Use `date +%F` — don't guess the date.
2. **Conventions:** invoke the `snakemake` skill before writing any pipeline, so
   the new experiment looks like its siblings.
3. **NOTE.md:** the front page — a reader understands the point without opening
   code. Title line + one-line takeaway, then `## Question` (hypothesis stated
   plainly: what confirms it, what refutes it), then `## Method` (fill in as the
   design takes shape).
4. **Issue:** auto-create it (user opted in — don't ask). Title = experiment;
   body = the question + folder path. Label `experiment-needed`. Report the
   number back. `gh issue create --title "..." --label experiment-needed --body "..."`

## Stage 2 — Run and record

As the experiment progresses, keep `NOTE.md` current: fill `## Method`, add a
`## Findings` section with the answer. The note grows with the work; it should
always read as the standalone story of the experiment.

## Stage 3 — Advance the label

The issue's label tracks the stage. Move it when the work moves (one label):

- `experiment-needed` — needs running/re-running (computation, not prose).
- `ready-to-paper` — results exist, ready to present in the paper. Create the
  label once if missing: `gh label create ready-to-paper --description "Results ready to present in the paper" --color 0e8a16`
- `documentation` — the task is writeups/notes/READMEs, not experiments.

Swap labels with `gh issue edit <n> --remove-label <old> --add-label <new>`.
Close the issue when the thread is done.

## Designing the experiment — keep it simple

Favor the simplest design that answers the question. Working principles:

> Beautiful > ugly. Explicit > implicit. Simple > complex. Complex > complicated.
> Flat > nested. Readability counts. If the idea is hard to explain, it's a bad
> idea; if it's easy to explain, it may be a good one.

If you can't explain the experiment to the user in a couple of plain sentences,
it's too complicated — cut it down before running anything.

## Talking about results

The user reads conclusions, not code. When reporting a finding:

- **Brief.** Lead with the answer; a few sentences beat a wall of text.
- **No invented acronyms.** Use plain words, not shorthand they must decode.
- **No code-speak.** Say what a thing *is*, never its variable/function/column name.
- **Standalone.** Explain so it makes sense to someone who never opened the files.

Test: would it make sense read aloud to someone with the research context but who
hasn't seen your code? If not, rewrite it.
