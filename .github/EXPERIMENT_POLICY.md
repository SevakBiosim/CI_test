# Experiment tracking policy

Every experiment is tracked as **one GitHub issue**. This document is the
convention; the [issue template](ISSUE_TEMPLATE/experiment.yml) enforces the structure.

## Title

`[System][Method] short description` — e.g. `[TYK2][ABFE] Restraint atom selection`.

The brackets are a scanning aid, not a search mechanism: they show up in
notification emails, Slack unfurls, `gh issue list`, browser tabs and
cross-references from other issues, none of which render labels. Precise
filtering is what the `system:*` / `method:*` labels are for — GitHub's search
normalises punctuation, so `in:title ABFE` matches with or without the brackets.

**One value per bracket, no lists.** A hyphenated compound is one value
(`[ABFE-vs-RBFE]`); `[ABFE+REST2]` and `[TYK2,MCL1]` are not — a list destroys
the scannability that is the only reason the brackets exist. The title carries
the primary axis; completeness lives in the labels and the Target set(s) field,
which have no width limit.

Two brackets, not three. Nothing enforces any of this — GitHub does not parse
titles — so it is a convention that holds only as long as we maintain it. A
third bracket starts crowding out the description, which is the part that
actually says what the issue is.

| Case | Title | Labels |
|------|-------|--------|
| One of each | `[TYK2][ABFE] Restraint atom selection` | `system:tyk2`, `method:abfe` |
| Many systems | `[JACS-8][RBFE] Charge scheme comparison` | one `system:*` each |
| Comparing methods | `[TYK2][ABFE-vs-RBFE] Convergence at fixed cost` | `method:abfe`, `method:rbfe` |
| Method on method | `[TYK2][ABFE] REST2 on the softcore windows` | `method:abfe`, `method:rest2` |
| No system | `[MDSuite][FEP] Lambda schedule default is asymmetric` | `type:finding` |

For several systems, name the set rather than enumerating it.

**Stacked methods** — where one technique is applied inside another, such as
REST2 on the windows of an ABFE calculation, or HREX within FEP — take the outer
method in the bracket, because that is what is being computed, and both in the
labels. `[TYK2][ABFE] REST2 on the softcore windows` already names REST2 in the
description, so the bracket does not need to; and the `method:rest2` label is
what makes "everything involving REST2" a findable query.

If an issue spans several systems *and* several methods and would produce a
handful of independent verdicts, split it: one issue per comparable unit. A
single Current status cannot summarise six outcomes. But if the systems are a
set that yields one number — RMSE across JACS-8 — that is one experiment.

## Labels

| Label | Values |
|-------|--------|
| `system:*` | one per system under study — apply as many as apply |
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

**`status:*` describes open issues only.** A closed issue keeps whatever status
it had, so "closed + `status:active`" is normal and means nothing — do not read
it, and do not bother clearing it at close. Always scope status queries with
`is:open`:

```
is:open label:status:blocked
```

A rule to remember degrades better than an action to remember: forget the rule
and you get a confusing query once, forget the action and the repo fills with
labels that are quietly wrong.

**Assign an owner.** The assignee is who provides the weekly update.

## Creation

Use the issue template so every experiment issue has the same structured fields
and nobody has to guess what to write.

## Top post — the spec

Purpose, success criteria, target set(s), baseline, configuration, and where the
data is stored. Keep it accurate: **if the plan pivots, edit the top post**
rather than letting it go stale.

Three fields deserve emphasis.

**Success criteria** are decided *before* running. State the threshold that would
make us adopt or reject the change, so the conclusion cannot be
reverse-engineered from the data once it is in.

**Baseline** may legitimately be `None` — a first measurement, or an exploratory
characterisation with nothing prior to compare against. Say so explicitly rather
than leaving it blank: it tells readers not to expect a delta, and it means the
success criteria have to be written as "what would count as having characterised
this well enough to decide" instead of a threshold.

**Configuration** does not restate the run parameters. Every run writes an
`output_config.json`, and that file is the ground truth — a hand-typed copy in
the issue is strictly worse, because it goes stale and can simply be wrong. Put
only what the JSON cannot say:

- **What was varied between runs** — the point of the experiment, and the one
  thing no single config file reveals. "Restraint atoms: CA-only vs. all heavy
  atoms, everything else fixed."
- **MDSuite version(s)** — so a reader can tell at a glance whether a
  version-specific bug applies, without downloading the data.
- **Anything done outside the tool** — a patched branch, hand-edited inputs, a
  system built by hand. This is the only category that is invisible everywhere
  else, so it matters most.

This also settles mid-experiment version bumps: each run's config carries its own
`MDSuite` field, so the run-to-build mapping is automatic. What still needs human
judgement is whether to *pool* the runs — if the update could plausibly move the
numbers, runs from either side of it are not the same condition and do not belong
in one mean. Say so in that week's update and either re-run the older ones or
report the two sets separately.

## Top post — living summary

Whenever there is a meaningful new conclusion, update the **Current status**
section at the top of the post, not just the comments below. Nobody should have
to read the whole comment history to know where things stand: comments are the
audit trail, the top post is the TL;DR.

## Updates — one comment each

What was tested, delta vs. baseline, conclusion. A few sentences, not a report.

- Max ~2 plots or tables per update; pick the ones that carry the point.
- A Claude Artifact link is fine for the deep dive, but **the comment must be
  self-contained** — never assume anyone clicks through.

## Data location

Always provide a link or path to the uploaded data needed to reproduce or
inspect the experiment — including the `output_config.json` files the
Configuration field defers to.

**Append to this field, never overwrite it.** One dated line per upload. New data
generated during an update gets a line here as well as a mention in the update
comment, so the top post stays the one place a reader finds everything rather
than pointing only at the most recent batch.

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

This lets us track the cost of each experiment over time and decide whether more
compute is justified.

## Closing

Rewrite **Current status** as the final answer — the outcome and the magnitude
of its impact — and close the issue.

There is no separate `Result` field. Current status is already the top post's
TL;DR and is rewritten at every conclusion, so at close it simply becomes the
last one; a second field would say the same thing, and would sit empty in the top
post for the whole life of every open issue. Closing is the rest of the record:
no status label to set.

## Adding labels

**Per issue.** The template applies `type:experiment` and `status:active` on its
own. The rest you set by hand: on the new-issue page, use the **Labels** picker
in the right sidebar before submitting, or open the issue and use the same picker
afterwards — start typing `system:` or `method:` to filter. Both need write
access to the repo.

A label that does not exist yet is **silently dropped**, not created — including
the two the template tries to apply. If your labels do not stick, they have not
been created yet.

Labels are **repository-wide, not personal**: creating one adds it to everyone's
picker, and deleting one strips it from every issue that used it, irreversibly.
It is also an unvalidated namespace — `system:TYK2` and `system:tyk2` are
two different labels that filter differently, and nothing warns you. So:
**lowercase, hyphenated, always** (`system:tyk2`, `method:abfe`). Check the
existing list before inventing a name.

**Creating them, once.** In the browser: **Issues -> Labels -> New label**.
Or, with `gh` authenticated:

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
```

Add a `system:<name>` label as each new system comes up — no need to define them
up front:

```bash
gh label create system:tyk2 -R $REPO -c '#BFD4F2'
```
