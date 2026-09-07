# Experiment tracking policy

One GitHub issue per experiment. The
[issue template](ISSUE_TEMPLATE/experiment.yml) carries the required fields.

**Assign one directly responsible owner.** Other contributors may also be
assigned, but the primary owner is responsible for the weekly update.

## Title

`[System][Method] short description`. One value per bracket, no lists, two
brackets at most — the point is that a list of issues can be skimmed down its
left edge. Everything that does not fit goes in the labels and the Target set(s)
field. Use `[MDSuite]` when no simulated system is involved.

| Case | Title | Labels |
|------|-------|--------|
| One of each | `[TYK2][ABFE] Restraint atom selection` | `system:tyk2`, `method:abfe` |
| Many systems | `[JACS-8][RBFE] Charge scheme comparison` | one `system:*` each |
| Comparing methods | `[TYK2][ABFE-vs-RBFE] Convergence at fixed cost` | `method:abfe`, `method:rbfe` |
| Method on method | `[TYK2][ABFE] REST2 on the softcore windows` | `method:abfe`, `method:rest2` |
| No system | `[MDSuite][FEP] Lambda schedule default is asymmetric` | `type:finding` |

Split an issue that spans several systems *and* several methods into one issue
per comparable unit — a single Current status cannot summarise six verdicts.

## Labels

| Label | Values |
|-------|--------|
| `system:*` | one per system under study — apply as many as apply |
| `method:*` | `abfe`, `rbfe`, `fep`, `rest2`, `md`, `analysis` |
| `status:*` | `active`, `blocked`, `paused` |
| `type:*` | `experiment`, `finding` |
| `outcome:*` | `adopted`, `rejected`, `inconclusive` — set once, at close |

Lowercase and hyphenated, always. `type:finding` is a standalone bug or
observation rather than a running experiment; only experiments have a template.
There is no `status:done` — a closed issue is done.

## Top post

The spec: purpose, success criteria, target set(s), baseline, and data location.
**If the plan pivots, edit the top post** rather than letting it go stale.

- **Success criteria** are decided *before* running, as a number where possible,
  so the conclusion cannot be reverse-engineered from the data. Exploratory
  experiments may use decision criteria rather than pass/fail thresholds, but
  those criteria must still be written before examining the result.
- **Baseline** may be `None` — a first measurement with nothing to compare
  against. Say so explicitly; then the success criteria state what would count
  as having characterised the thing well enough to decide.
- **Data location** is appended to, never overwritten — one dated line per
  upload, so the top post stays the one place to find everything.
- **Current status** is the top post's TL;DR, rewritten at every meaningful
  conclusion and dated. Nobody should have to read the comment history to know
  where things stand.

## Updates

One comment per update: what was tested, delta vs. baseline, conclusion. A few
sentences, not a report. At most ~2 plots or tables. A link to a detailed report
or interactive artifact is fine for the deep dive, provided it is accessible to
the team and retained as long as the experiment record — but the comment must
stand on its own.

Post an update when there is something to report rather than saving it for the
end of the week. Every `status:active` issue receives **at least** one update
each week, including approximate core-hours since the last update and the
machine(s); take the core-hours from the scheduler rather than estimating. If
there is no new result, briefly report what is running, what is blocking
progress, or that there was no material change. If work is not expected during
the coming week, set `status:paused`. Use `status:blocked` when progress is
waiting on something external; a blocked issue still gets its weekly update,
naming what is being waited on.

Run parameters live in each run's `output_config.json` and are not restated.
What that file cannot show goes in the update covering those runs: the MDSuite
version, and anything done outside the tool — a patched branch, hand-edited
inputs. If it changes how the results should be read, put it in **Current
status** as well.

## Closing

Rewrite **Current status** as the final answer — outcome and magnitude of impact
— then close the issue, remove any `status:*` label, and set one `outcome:*`
label. There is no separate Result field; Current status is already rewritten at
every conclusion, so at close it becomes the record.

- `outcome:adopted` — the change works and we are taking it.
- `outcome:rejected` — the proposal did not work.
- `outcome:inconclusive` — the experiment could not answer the question:
  insufficient statistical power, or characterisation work with no proposed
  change to accept or reject.

Keep those last two apart. "It did not work" and "we could not tell" lead to
different decisions about whether to try again.

An experiment abandoned rather than concluded is closed as **not planned**, with
no outcome label.
