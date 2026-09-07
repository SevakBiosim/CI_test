# Experiment tracking policy

One GitHub issue per experiment. The
[issue template](ISSUE_TEMPLATE/experiment.yml) carries the required fields.

## Title

`[System][Method] short description`. One value per bracket, no lists, two
brackets at most — the point is that a list of issues can be skimmed down its
left edge. Everything that does not fit goes in the labels and the Target set(s)
field. Use `[MDSuite]` or `[Infra]` when no simulated system is involved.

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
| `outcome:*` | `adopted`, `rejected` — set once, at close |

Lowercase and hyphenated, always. `type:finding` is a standalone bug or
observation rather than a running experiment; only experiments have a template.
There is no `status:done` — a closed issue is done.

Assign an owner. The assignee provides the weekly update.

## Top post

The spec: purpose, success criteria, target set(s), baseline, configuration, and
data location. **If the plan pivots, edit the top post** rather than letting it
go stale.

- **Success criteria** are decided *before* running, as a number where possible,
  so the conclusion cannot be reverse-engineered from the data.
- **Baseline** may be `None` — a first measurement with nothing to compare
  against. Say so explicitly; then the success criteria state what would count
  as having characterised the thing well enough to decide.
- **Configuration** does not restate run parameters: each run's
  `output_config.json` is the ground truth. Record only what was varied between
  runs, the MDSuite version(s), and anything done outside the tool.
- **Data location** is appended to, never overwritten — one dated line per
  upload, so the top post stays the one place to find everything.
- **Current status** is the top post's TL;DR, rewritten at every meaningful
  conclusion and dated. Nobody should have to read the comment history to know
  where things stand.

Each run's config records the MDSuite version it ran under. Runs from either side
of a version bump that could move the numbers are not the same condition and do
not belong in one mean.

## Updates

One comment per update: what was tested, delta vs. baseline, conclusion. A few
sentences, not a report. At most ~2 plots or tables — a Claude Artifact link is
fine for the deep dive, but the comment must stand on its own.

Every open issue with something new gets an update each week, including
approximate core-hours since the last update and the machine(s). Take the
core-hours from the scheduler rather than estimating.

Not being worked on? Set `status:paused`, which drops it out of the weekly
sweep. The sweep also checks for issues still marked `status:active` that have
gone quiet:

```
is:open label:status:active updated:<YYYY-MM-DD
```

## Closing

Rewrite **Current status** as the final answer — outcome and magnitude of impact
— then close the issue and set `outcome:adopted` or `outcome:rejected`. There is
no separate Result field; Current status is already rewritten at every
conclusion, so at close it becomes the record.

An experiment abandoned rather than concluded is closed as **not planned**, with
no outcome label.
