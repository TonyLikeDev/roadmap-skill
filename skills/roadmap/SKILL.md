---
name: roadmap
description: Run a project from a live status board. Use when a repo has a ROADMAP.md (or its CLAUDE.md/AGENTS.md mentions one) and the user asks what to do next or where things stand, wants to continue or finish an item, tick or log work, close a week or phase, or get a progress report; also when the user asks to set up a roadmap or plan tracking for a project. Covers setup, the work loop, honest checkboxes, a generated progress bar, the change log, notes and deviations, and the git rules that go with them.
argument-hint: "[init | status | next | tick | report]"
---

# Roadmap

A small, strict way to run a multi-week project with an AI assistant. Three ideas:

- The **plan** (a document) holds the schedule and the decisions. It does not change when work gets done.
- **ROADMAP.md** is the only place progress is recorded: checkboxes per unit (week, phase, sprint), a change log, an ordered list of next actions.
- A script **generates the progress bars** from the checkboxes, so nobody maintains them by hand.

Finishing an item always leaves three traces in the same change: a ticked box, a dated change-log line, a refreshed progress block.

Bundled here (paths are relative to the skill's base directory, which is shown when it loads):
- `scripts/roadmap_progress.py`: regenerates the progress block. Python 3.9+, no packages. `--check` exits 1 when the block is stale and writes nothing; `--selftest` verifies the script itself.
- `templates/ROADMAP.md`, `templates/PLAN.md`, `templates/unit.md`, `templates/claude-md-section.md`.

## Pick the mode

An argument picks the mode (`/roadmap:roadmap init`); a plain request like those in the second column picks the same mode. With neither: if ROADMAP.md exists, do **Status**; otherwise offer **Init** and wait for a yes.

| Argument | The user says | Mode |
| :--- | :--- | :--- |
| `init` | "set up a roadmap", "track this project" (no ROADMAP.md yet) | **Init** |
| `status` | "what's next", "where are we", "what should we do now" | **Status**: read, then recommend; do not start work |
| `next` | "continue", "do the next item", "build X" where X is on the roadmap | **Work loop**: do the next item |
| `tick` | "tick that", "log it", "close week 3", "I pushed", "refresh the progress" | **Bookkeeping**: record work already done; build nothing |
| `report` | "write a progress report" | **Progress report** |

## The legend

- `[x]` done and **verified**: the test ran, the number was measured, the file was opened and checked, the git state was looked at.
- `[~]` partly done, or written but not verified. Say what is missing on the line itself. Counts as half.
- `[ ]` open.

Never tick on intention. When in doubt, `[~]`.

## What ROADMAP.md looks like (`templates/ROADMAP.md`)

1. Title, then the rule paragraph: this file is the state, the plan is the plan.
2. The legend and `Last updated: DATE (machine or session)`.
3. `## Progress`: the generated block between `<!-- progress:start unit=Week total=10 -->` and `<!-- progress:end -->`. Never edit between the markers.
4. One `## Week N: title` section per started unit, with `### Build`, `### Measure`, `### Write`, `### Exit criteria`, and `### Notes and deviations` once there is something to say. Later units stay one compact `## Weeks N to M` block; expand each into a full section, copying its items from the plan, when it starts. Only `## <Unit> N` sections are counted, and a section ends at the next `## ` heading.
5. `## Immediate next actions (in order)`: numbered. Tag **User action** for what only the user can do (commit, push, run on another machine) and give the exact command.
6. `## Change log`: append-only, dated, tagged `(machine or session)`; what was done, the key numbers, what was found, what stays open.

The subsection names fit research and build projects; rename them to fit the project, but keep Exit criteria and Notes and deviations. The unit word is one word (Week, Phase, Sprint) and is set in the start marker.

## Init

1. Look at what exists: ROADMAP.md (if present, never overwrite it: do Status instead), plan documents, README, CLAUDE.md, AGENTS.md, the test and run commands.
2. Find the plan. If a plan document exists, derive from it. If not, ask where the plan lives or what the units and goals are (one question), and offer to write a `PLAN.md` first from `templates/PLAN.md` (objectives, decisions, the schedule per unit, risks). Decide the unit word, the number of units, the titles.
3. Create `ROADMAP.md` from `templates/ROADMAP.md`. Fill every `{{placeholder}}` and every `<prompt>`; afterwards `grep -nE '\{\{|<[a-z]' ROADMAP.md` should find only real content (the `<!--` markers do not match). Write the first unit (and the next, if it is concrete) as full sections with items from the plan, everything later as the compact block. An item is concrete and checkable: a file, a test, a number, a decision.
4. Copy `scripts/roadmap_progress.py` into the project's `scripts/` (create it if needed) so every machine and CI can run it without this skill. Run it, then run it with `--check`.
5. Add the workflow to the project's CLAUDE.md: append the blocks of `templates/claude-md-section.md` that apply (create the file if missing; never rewrite what is there; skip a block already present). If other agents' tools are in use, add `AGENTS.md`: "All instructions for AI coding agents live in `CLAUDE.md`. Read it, then open `ROADMAP.md` for the current status and the next open item."
6. Ask once about git: default is the template's rules (the assistant never commits or pushes; no AI attribution). With the default, also create or merge `.claude/settings.json` with `{"attribution": {"commit": "", "pr": ""}}`, keeping any keys already there. If the user declines, skip both the git-rules block and the settings.
7. If the project measures things, include the experiments block. If the user wants a file per unit, create them from `templates/unit.md`.
8. Report: the files created, the units and item counts, the first item to take. Leave everything uncommitted and suggest a commit message.

## Status

Read ROADMAP.md (progress block, current unit, Immediate next actions, the last change-log entries), then `git status -sb` and `git log -3` to see what happened since the last entry. Run the script with `--check`; if the block is stale, say so. Answer in a few lines: where the project stands, what is blocked on a user action, and **one** recommended next item with the reason. Do not start work and do not edit the roadmap.

## Work loop

1. Read ROADMAP.md, then the project's CLAUDE.md and the plan's section for the item.
2. Take the next open item of the current unit, or the one the user names. Say which.
3. Build it. Verify it the way the project verifies (tests, a run, a measurement). A measured number goes into a logged run; prose goes where the project keeps its write-ups.
4. Record it: tick the box (`[x]` or `[~]` per the legend, with specifics: file names, test counts, numbers, where the result lives), add the change-log line, update Immediate next actions and `Last updated`, and note anything surprising under the unit's Notes and deviations.
5. Run `scripts/roadmap_progress.py` with the project's Python command (`python3`, `python` or `py`; the CLAUDE.md section names it). It rewrites the file, so re-read ROADMAP.md before the next edit.
6. Leave the changes uncommitted. Close with: what was ticked and the new progress, a suggested commit message that names the unit and the item, and the next item.

## Bookkeeping

- Ticking follows the work-loop rules. Git facts (pushed, merged, clean) are ticked only after looking: `git status -sb`, `git ls-remote origin <branch>`, `git log`.
- Closing a unit: every box is `[x]`, or `[~]` / `[ ]` with the reason under Notes and deviations; exit criteria ticked; the unit file, if any, final; then expand the next unit from the plan.
- Changing scope: Notes and deviations first, the plan document after.
- End every edit with the script.

## Progress report

Assemble it from the change log, the ticked items and the unit files for the period: what was done, the numbers (only from logged runs), what is next, the risks. Do not invent. Save it where the project keeps reports, or give it in chat when it is short.

## Rules that must not drift

- The plan holds the plan, ROADMAP.md holds the state. Never record progress in the plan.
- Never edit the generated block and never hand-write a percentage.
- Every finished item gets a tick, a change-log line and a refreshed block, in the same change.
- The change log is append-only and dated; tag the machine or session when more than one is in use.
- Numbers in the roadmap and in write-ups come from logged runs. A number from a quick check goes into a logged run or stays in chat.
- Never tick what was not verified.

## Git rules (defaults; the project's CLAUDE.md wins)

Never commit, amend, rebase, tag or push unless the user asks for that specific action in the current message. Add no AI attribution anywhere (no `Co-Authored-By`, no "Generated with" lines). Suggest the commit message instead.

## Pitfalls (each of these happened)

- `[x]` for "written, not run" or "mostly done": use `[~]` and say what is missing.
- A step that only the user can do (commit, push, run on the other machine) left implied: write it as **User action** with the command.
- A quick check's number copied into the docs: log it as a run, or keep it in chat.
- Units written out in full long before they start: they go stale. Keep them compact until they start.
- The script's block is overwritten on every run: edit around it, never inside it.
- Bars that look wrong: only `## <Unit> N` headings count, a section ends at the next `## `, and the total is `total=` in the start marker or else the highest unit number seen (`## Weeks 6 to 10` counts as 10).

## Optional: experiments, unit files, several machines

- **Experiments** (the block in `templates/claude-md-section.md`): one folder per run, `<experiments dir>/<date>_<kind>_<name>/`, with `config.yaml` (settings, seed, device, git commit and dirty flag), `results.csv` and the main artifact; never reuse a folder, add a numeric suffix. Heavy outputs are not committed.
- **Unit files**: `<experiments dir>/week_NN.md` from `templates/unit.md` mirror a roadmap section with the measured results; the roadmap stays the source of truth for status.
- **Several machines**: tag change-log entries `(macbook)`, `(desktop)`; add a `Machines:` line to the legend; log the commit hash in every run; never assume the other machine has pulled; cross-machine steps are **User action** items. Install the plugin on the other machine if you want the skill there; the project's own CLAUDE.md snippet and `scripts/roadmap_progress.py` work without it.
