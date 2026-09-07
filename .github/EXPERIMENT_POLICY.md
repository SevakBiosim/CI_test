# Experiment tracking policy

Every experiment is tracked as **one GitHub issue**. This document is the
convention; the [issue template](ISSUE_TEMPLATE/experiment.yml) enforces the structure.

## Title

`[System][Method] short description` — e.g. `[A71EV2A][ABFE] Restraint atom selection`.

The brackets double as searchable tags even without labels. Use `[MDSuite]` or
`[Infra]` in the system slot when no simulated system is involved.

## Labels

| Label | Values |
|-------|--------|
| `system:*` | one per system under study |
| `method:*` | `abfe`, `rbfe`, `fep`, `rest2`, `md`, `analysis` |
| `status:*` | `active`, `blocked`, `paused` |
| `type:*` | `experiment`, `finding` |

`type:experiment` is a running experiment. `type:finding` is a standalone bug
report or observation — not an experiment, and worth filtering separately. Only
experiments have a template; open a finding as a blank issue and label it.

There is no `status:done`: a closed issue *is* done, and a second place to record
it only gives us two things to keep in sync. The three status values distinguish
states an open issue cannot otherwise express — `blocked` is waiting on something
external, `paused` is deliberately deprioritised.

**Assign an owner.** The assignee is who provides the weekly update.

## Creation

Use the issue template so every experiment issue has the same structured fields
and nobody has to guess what to write.

## Top post — the spec

Purpose, success criteria, target set(s), fixed config (force field, timestep,
repeat count, ...), MDSuite version and exact invocation, baseline, and where the
experiment data is stored. Keep it accurate: **if the plan pivots, edit the top
post** rather than letting it go stale.

Two fields deserve emphasis.

**Success criteria** are decided *before* running. State the threshold that would
make us adopt or reject the change, so the conclusion cannot be
reverse-engineered from the data once it is in.

**MDSuite version and exact invocation** — tag or commit, plus the command line.
Defaults change over time, so a prose description of the config is not enough to
re-run an experiment a year from now. If you move to a newer MDSuite partway
through, add a line to the field rather than overwriting it, so which runs used
which build stays recoverable:

```
- v0.2.6 (307e89c): windows 1-20, all runs through 2026-09-10
- v0.2.7 (abc1234): re-runs of windows 8-12 from 2026-09-12
```

And treat the bump as a change in conditions, not bookkeeping: if the update
could plausibly move the numbers, runs from either side of it are not the same
condition and should not be pooled into one mean. Say so in that week's update
and either re-run the older ones or report the two sets separately.

## Top post — living summary

Whenever there is a meaningful new conclusion, update the **Current status**
section at the top of the post, not just the comments below. Nobody should have
to read the whole comment history to know where things stand: comments are the
audit trail, the top post is the TL;DR.

## Updates — one comment each

What was tested, delta vs. baseline, conclusion. A few sentences, not a report.

- Quote every free energy value with its uncertainty and the number of repeats —
  `-9.4 +/- 0.3 kcal/mol (n=3)`. Where the `+/-` is not the std across repeats
  (an MBAR analytical error, say), name it: the two are different claims and are
  easy to confuse.
- Max ~2 plots or tables per update; pick the ones that carry the point.
- A Claude Artifact link is fine for the deep dive, but **the comment must be
  self-contained** — never assume anyone clicks through.

## Data location

Always provide a link or path to the uploaded data needed to reproduce or
inspect the experiment. If new data is generated during an update, say where it
was uploaded.

## Weekly cadence

Before the team meeting, update every open issue that has something new. A Slack
reminder triggers this.

If nothing changed, you do not owe a comment every week — a wall of "no update
this week" is noise people learn to skip, which defeats the point. Instead:

- **Silent for 2+ weeks?** Leave a one-line note saying where it stands and
  what it is waiting on. That tells readers the issue is alive, not abandoned.
- **Not going to be worked on for a while?** Label it `status:paused` and say
  why. The label carries that better than a repeated comment does, and it drops
  the issue out of the weekly sweep until it comes back.

Each weekly update also reports **compute usage since the previous update**:
approximate core-hours and the machine(s) (Archer2, Cirrus, OVH, ...). Take the
number from the scheduler rather than estimating it — e.g.

```bash
sacct -S 2026-09-01 -u "$USER" -X --format=JobID,JobName,Elapsed,AllocCPUS,AllocTRES
```

Report GPU-hours separately from core-hours; they are not interchangeable. This
lets us track the cost of each experiment over time and decide whether more
compute is justified.

## Closing

Edit the top post to fill in the `Result` section — final outcome and magnitude
of impact — and close the issue. Closing is the whole record; there is no status
label to set.

## Creating the labels

One-time setup, for whoever has `gh` authenticated:

```bash
REPO=SevakBiosim/CI_test
gh label create type:experiment -R $REPO -c '#1D76DB' -d 'A running experiment'
gh label create type:finding    -R $REPO -c '#5319E7' -d 'Bug or observation worth recording'
gh label create status:active   -R $REPO -c '#0E8A16' -d 'Being worked on'
gh label create status:blocked  -R $REPO -c '#D93F0B' -d 'Waiting on something external'
gh label create status:paused   -R $REPO -c '#FEF2C0' -d 'Deliberately deprioritised'
gh label create method:abfe     -R $REPO -c '#FBCA04'
gh label create method:rbfe     -R $REPO -c '#FBCA04'
gh label create method:fep      -R $REPO -c '#FBCA04'
gh label create method:rest2    -R $REPO -c '#FBCA04'
gh label create method:md       -R $REPO -c '#FBCA04'
gh label create method:analysis -R $REPO -c '#FBCA04'
# then one `system:<name>` label per system, as they come up
```
