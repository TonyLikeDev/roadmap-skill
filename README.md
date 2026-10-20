# roadmap

A Claude Code plugin that runs a multi-week project from one live file, `ROADMAP.md`. Claude reads it to know what comes next, and updates it every time it finishes something: a ticked box, a dated change-log line, and progress bars generated from the checkboxes.

What the top of a roadmap looks like:

**Progress: 32% of the 4-week plan** `[██████░░░░░░░░░░░░░░]` · Weeks 1-2 in progress: 65% `[█████████████░░░░░░░]`

| Week | Items | Done | Progress | |
| :--- | ---: | ---: | :--- | ---: |
| Week 1 | 4 | 4 | `[████████████████████]` | 100% |
| Week 2 | 5 | 1.5 | `[██████░░░░░░░░░░░░░░]` | 30% |
| Week 3 | - | - | `[░░░░░░░░░░░░░░░░░░░░]` | not started |
| Week 4 | - | - | `[░░░░░░░░░░░░░░░░░░░░]` | not started |

## Why

Long projects with an AI assistant go wrong in the same few ways:

- Progress lives in chat history, so every new session starts with re-explaining where things stand.
- "Done" means "I wrote it", not "I ran it and it works".
- Status gets written into the plan until nobody can tell the plan from the state.
- Percentages are typed by hand and stop matching reality.

The plugin answers each one with a rule:

- **The plan and the state are separate files.** `PLAN.md` (or the plan you already have) holds the schedule and the decisions. `ROADMAP.md` is the only place progress is recorded.
- **Checkboxes are honest.** `[x]` means verified: the test ran, the number was measured. `[~]` means written but not verified; it counts as half and says what is missing. `[ ]` is open.
- **Progress bars are generated** from the checkboxes by a small script. Nobody types a percentage.
- **Every finished item leaves three traces** in the same change: the tick, the change-log line, the refreshed bars.

## Install

```
/plugin marketplace add TonyLikeDev/roadmap-skill
/plugin install roadmap@roadmap
```

To try a local clone instead: `/plugin marketplace add /path/to/roadmap-skill`

## Use

Pass a mode, or just ask:

| Command | Or say | What happens |
| :--- | :--- | :--- |
| `/roadmap:roadmap init` | "set up a roadmap for this project" | Builds `ROADMAP.md` from your plan (or helps write `PLAN.md` first), copies the progress script into `scripts/`, adds the workflow to `CLAUDE.md` |
| `/roadmap:roadmap status` | "where are we?", "what's next?" | Reads the roadmap and git, recommends one next item, changes nothing |
| `/roadmap:roadmap next` | "continue", "do the next item" | Builds and verifies the next open item, then ticks it, logs it and refreshes the bars |
| `/roadmap:roadmap tick` | "tick that", "close week 3" | Records work already done; builds nothing |
| `/roadmap:roadmap report` | "write a progress report" | Writes a report from the change log and ticked items, nothing invented |

A unit can be a week, a phase or a sprint; the word is set once in `ROADMAP.md`.

## What it adds to your project

- `ROADMAP.md`: the status board. A full section per started unit (Build, Measure, Write, Exit criteria, Notes and deviations), later units kept compact until they start, an ordered list of next actions, and an append-only change log.
- `scripts/roadmap_progress.py`: regenerates the progress block. Python 3.9+, standard library only, so it runs without the plugin, on any machine and in CI.
- A "Roadmap workflow" section in `CLAUDE.md`, plus an `AGENTS.md` pointer when other coding agents are in use, so the rules hold even where the plugin is not installed.
- Optional: a `PLAN.md`, a file per unit, and rules for logging experiment runs in projects that measure things.

The script on its own:

```
python scripts/roadmap_progress.py             # refresh the progress block
python scripts/roadmap_progress.py --check     # exit 1 if the block is stale (CI, pre-commit)
python scripts/roadmap_progress.py --selftest  # check the script itself
```

## Git defaults

Init adds two rules to `CLAUDE.md` unless you decline when it asks: Claude never commits or pushes on its own (it suggests a commit message instead), and it adds no AI attribution to commits or PRs.
