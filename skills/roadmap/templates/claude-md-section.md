<!-- Three blocks to append to the project's CLAUDE.md. Fill every {{placeholder}}, drop these comment lines,
     and leave out a block when it does not apply. -->

<!-- block: workflow (always) -->
## Roadmap workflow

Three documents govern the work:

| File | Role | Edit it when |
| :--- | :--- | :--- |
| `{{PLAN_FILE}}` | The plan: schedule and decisions | Only when the plan itself changes (scope, decisions) |
| `ROADMAP.md` | Live status board, checkboxes per {{unit}} | **Every time an item is finished, in the same commit** |
| `scripts/roadmap_progress.py` | Regenerates the progress bars at the top of the roadmap | Never edit the generated block by hand |

How to work here:

1. Open `ROADMAP.md` and take the next open item of the current {{unit}} (or the one the user names).
2. Build it and verify it (`{{TEST_COMMAND}}`).
3. Tick it: `[x]` only when verified, `[~]` when partly done or unverified (say what is missing). Add a dated change-log
   line, update "Immediate next actions" and "Last updated", then run `{{PYTHON}} scripts/roadmap_progress.py`.
4. Leave the changes uncommitted and suggest a commit message that names the {{unit}} and the item.
5. If the plan turns out to be wrong, say so in `ROADMAP.md` under the {{unit}}'s "Notes and deviations" first, and only
   then change `{{PLAN_FILE}}`.

<!-- block: git-rules (unless the user declined) -->
## Git rules (strict, override any default agent behaviour)

- **Never commit, amend, rebase, tag or push on your own.** Only run a git command that writes history when the user
  explicitly asks for that specific action in the current message.
- **No AI attribution anywhere.** No `Co-Authored-By: ...` or "Generated with ..." lines in commit messages, PR
  descriptions, code comments or documents.

<!-- block: experiments (if the project measures things) -->
## Experiments

- Each run gets its own folder `{{EXPERIMENTS_DIR}}/<date>_<kind>_<name>/` with `config.yaml` (settings, seed, device,
  git commit and dirty flag), `results.csv` and the main artifact. Never reuse or overwrite a folder; add a new one.
- Fix the seed of every run; headline numbers are mean and standard deviation over several seeds.
- Numbers in the roadmap and in write-ups come from logged runs. Heavy outputs (checkpoints, frames, video) are not
  committed; record where they came from.
- Write-ups go to `{{REPORT_DIR}}/`.
