# Roadmap and status

Master schedule: `{{PLAN_FILE}}` ({{TOTAL}} {{unit}}s). This file is the live status board.
**Rule: whenever an item is finished, tick it here in the same commit**, and add a dated line
under "Change log". Never edit `{{PLAN_FILE}}` to record progress; it is the plan, this is the state.

Legend: `[x]` done and verified, `[~]` partly done or written but not verified (say what is missing), `[ ]` open.

Last updated: {{DATE}} ({{SESSION}}).

## Progress

<!-- progress:start unit={{Unit}} total={{TOTAL}} -->
<!-- progress:end -->

---

## {{Unit}} 1: {{title}}

### Build
- [ ] <a concrete, checkable item: a file, a test, a number, a decision>

### Measure
- [ ] <what is measured, and the logged run where the result will live>

### Write
- [ ] <what is written, and where it goes>

### Exit criteria
- [ ] <a condition that must hold before the next {{unit}} starts>

### Notes and deviations
- <plan corrections and surprises go here first; change `{{PLAN_FILE}}` only afterwards>

---

## {{Unit}}s 2 to {{TOTAL}}

Not started. See `{{PLAN_FILE}}` for the full list. When a {{unit}} starts, expand it into its own
`## {{Unit}} N: title` section here, copying its items from the plan.

---

## Immediate next actions (in order)

1. <the first concrete step; tag **User action** when only the user can do it, and give the exact command>

## Change log

- {{DATE}} ({{SESSION}}): roadmap created from `{{PLAN_FILE}}`.
