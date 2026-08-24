---
name: final-sweep
description: |
  One last polish pass over a finished change before it ships: hunt down code
  that reimplements something the language, a dependency, or the repo already
  provides, delete dead and orphaned code, collapse real repetition, audit
  every comment against a keep/kill test, run humanizer on the survivors,
  sanity-check file placement, then re-run every quality gate. Use when the
  user says "final sweep", "polish this up", "clean up before commit", "remove
  useless comments", "check for dead code", "one last pass", or when
  implementation work is done and the diff is about to be reviewed, committed,
  or PR'd. Not a bug hunt and not a refactor pass; it removes noise and
  reimplementation, and reports any edge-case delta a replacement introduces.
---

# Final sweep

You are doing the last pass over a completed change before someone else
reads it. The code already works and the tests already pass. Your job is to
remove everything that will waste the reviewer's attention: code that did not
need to be written because something already does it, code that no longer does
anything, and prose that says what the code already says.

The most expensive thing a sweep can miss is a function that should not exist.
A reviewer can skim a redundant comment in a second; a hand-rolled
reimplementation of a library they already ship costs them the rest of the
file's credibility. Comments are the cheapest pass and the least valuable one,
which is why they come fourth here and not first.

## Behavior: neutral by default, one carve-out

Deleting noise must never change behavior. But passes 1 and 3 replace code with
an equivalent, and equivalents differ at the edges: a library rounds the other
way at exactly zero, picks a different unit at exactly 24 hours, throws where
yours returned a default.

A replacement is allowed to shift edge-case output when the new behavior is
defensible on its own terms. What is not allowed is shipping that shift
silently. Enumerate the inputs where old and new disagree, decide each is
acceptable, and report them. If you cannot enumerate them, you do not
understand the replacement well enough to be making it.

## Scope

Get the file list from git, not from memory:

```bash
git diff --name-only <base-branch>...HEAD
git status --short   # plus anything uncommitted
```

**Read widely, edit narrowly.** Searching is unbounded: passes 1 and 2 must
read outside the diff, across the whole repo and into `package.json` and its
dependencies, or they cannot work at all. Editing is bounded: your edits land
only in the files the change touched. Do not clean up an untouched file just
because the search led you through it; note it and move on.

Inside the files the change touched, sweep every comment, not just the lines
the change added. Authorship is invisible to the reviewer: a pre-existing
comment in a file already in the diff wastes their attention exactly as much
as one you just wrote, and removing it is behavior-neutral and diff-cheap. "It
was already there" is not a reason to keep noise in a file you are already
editing.

Pre-existing *live* logic is the opposite: do not refactor, re-flow, or
re-scope working code the change did not touch. The one exception is a one-line
fix directly adjacent to your change (an unused import your edit exposed, for
example).

Dead code is not live logic, and this rule does not shelter it. Anything pass 2
proves is dead inside a swept file gets deleted, however long it has been
there: a return field nobody reads, a branch nothing can reach, an arg every
caller passes the same literal for. Deleting it is behavior-neutral by
definition, which is what puts it on the same footing as a comment and not on
the same footing as a refactor. "It predates the change" and "removing it edits
a signature" are both restatements of "it is dead", so neither one downgrades
the verdict to a flag.

Note in the report anything you swept that the change itself did not
introduce, so the reviewer knows why those lines moved.

## Pass 1: did this need to be written?

Start top-down. Every other pass pattern-matches on code shapes, which only
finds what you can already see; this one starts from what the change *added*
and asks whether it needed adding.

**Enumerate first.** List every capability the change introduced: each new
top-level function, constant, type, hook, component, schema field, config
value, literal table. Write the list down. It is this pass's work product, and
a sweep that reports no such list did not run this pass.

For each item, ask three questions in this order, cheapest answer first:

1. **Does the language or runtime already do this?** `Intl.RelativeTimeFormat`
   and `Intl.NumberFormat` for human-readable values, `URL` and
   `URLSearchParams` for string surgery, `structuredClone`, `Set` operations,
   `Object.groupBy`, `AbortController`. CSS instead of JS. A platform builtin
   costs nothing to adopt and nothing to maintain.
2. **Does a dependency we already ship do this?** Read `package.json`. A
   library already in the tree is close to free: no new dependency, no bundle
   delta, nothing for a reviewer to approve. Then grep for existing imports of
   it to confirm it is established on *this* side of the codebase, since
   frontend and backend often differ.
3. **Does our own code already do this?** Grep by value and shape, not name: a
   second `['var(--cat-1)', …]` array is a twin under any name, and so is a
   second `60 * 60 * 1000`.

**Search the capability, never the identifier.** This is the whole technique.
`formatFollowUpWait` matches nothing anywhere. "Format a duration as words"
finds a library function, an internal near-twin, and a platform API. Name what
the code *does* in domain-neutral words, then search those words.

Give each item a verdict:

- **REPLACE** — call the existing thing, delete yours.
- **KEEP** — nothing does this.
- **KEEP, DIVERGES** — something does this but its contract is wrong here.
  Name the divergence in one line in the report.

**Contract, not resemblance.** Only route through the existing thing when its
meaning matches your intent. Rounding direction, error behavior, unit
selection, and what happens at zero all count as meaning. An internal helper
that floors when you need ceil is a real divergence, not a nitpick: reporting
"we have `formatDuration` but it rounds down, which would promise a deadline
sooner than it opens" is a successful outcome for this pass, the same as a
replacement is.

Worked example. A change adds a 12-line `formatFollowUpWait(ms)` that converts
milliseconds to `"21 hours"` or `"20 minutes"`, called from a component and a
server error. Enumerated as "format a duration as words," it draws three hits:
`date-fns` `formatDistanceStrict` with `roundingMethod: 'ceil'`, already
imported two files away; an internal `formatDuration` that floors and returns
compound output, so it diverges; and `Intl.RelativeTimeFormat`, which needs a
unit chosen for it. Verdict REPLACE against the library, and the twelve lines
become three. Note the deltas: the library says `"1 day"` at exactly 24 hours
and `"5 seconds"` below a minute, so the swap carries a one-line clamp and the
`"1 day"` difference goes in the report.

## Pass 2: dead, orphaned, and vestigial code

The change deleted, renamed, or reshaped things. Find what it stranded.
This is line-by-line, not grep-and-move-on: read every symbol the swept
files touch and ask "does this still do work?" A symbol that compiles and
is referenced can still be dead: the linter and a name grep both say
"live" while the value never varies and the branch never fires. Those are
the ones that survive review and rot. Trace values, not just names.

1. For every function, export, file, or constant the change removed or
   replaced, grep the whole repo for remaining references. Include string
   references (scheduler targets, dynamic lookups), not just imports.
2. Check each swept file for exports with zero consumers. A public
   contract type used only internally is fine; a helper nobody calls is
   not.
3. When a file's last real export dies, delete the file, not just the
   export.
4. Let the linter catch unused imports, then actually run it.
5. **Vestigial values.** For each field, parameter, prop, config flag, or
   piece of state the change left behind, trace how it is written and read.
   Follow it to its consumers; a symbol that is written on every path and read
   on none is dead no matter how many places name it. Delete it when:
   - it is now only ever set to one constant value (the code that varied it
     is gone), so every branch reading it is effectively decided at compile
     time: delete the field and collapse the branch;
   - it is written but never read, or read but the read can no longer change
     any outcome;
   - it is a struct/interface field or function arg that every call site now
     passes the same literal for, or that nothing passes at all.
   Example: a `PhoneFilterConfig { excludeTypes, excludeEmpty }` where the
   UI that toggled `excludeEmpty` was replaced, so it is pinned to `false`
   everywhere and its `if (excludeEmpty …)` branch is unreachable. Grep
   shows it referenced in five places; it is still dead. Remove the field,
   the branch, and the now-constant literals.
   Some values are dead by construction rather than by accident, and those are
   the ones a reference count will never catch: the return type of a function
   only ever reached through a fire-and-forget scheduler, a result assembled on
   a path whose caller discards it. Ask where each value is consumed, not
   whether it is mentioned. Deleting one of these usually simplifies the body
   too, since the object being built was the only reason to hold the values
   that fed it.
6. **Reachability.** For each conditional the change touched, ask whether
   any real input can still take each side. A branch guarding on a
   now-constant flag, an `else` for a case the new types forbid, or an
   option in a menu nothing can select is dead: remove it.
7. **Semantics you changed by accident.** For each guard or early return the
   change added, ask what the code did before it existed. Adding a read to get
   at a field can quietly turn a throw into a silent no-op, or the reverse.
   Match the convention its siblings use, and say so in the report.

If something is intentionally kept despite being unreferenced or inert (a
deploy shim for pending scheduled jobs, a migration), it must have a comment
saying when it can be deleted and, when a tracker exists, the ticket ID.

## Pass 3: DRY, with restraint

Pass 1 already hunted for existing implementations across the repo and its
dependencies. This pass handles what is left: repetition *within* the change.

**Extract.** Only when the repetition is real:

- Three or more occurrences of the same multi-line block: extract a small
  local helper.
- Two occurrences: usually leave it. Two call sites reading plainly beat
  one abstraction plus indirection.
- Never extract across module boundaries just to deduplicate. Shared code
  needs a shared reason, not just shared shape. The exception is a twin pass 1
  turned up in another domain, where calling one from the other would couple
  them: extract both to a shared neutral home, which is a genuine shared
  reason.

**One occurrence: inline it back.** A constant, validator, type, or helper
with exactly one reference is not deduplication, it is a name plus a jump.
Walk the top-level declarations in each swept file, count real references,
and inline the singletons. A four-line `sourceValidator` union used by one
mutation's `args` belongs inside those args; pulling it out only makes the
reader scroll up to learn what the inline form already said.

Single use is the trigger for the question, not the verdict. Keep the name
when it carries something the inline form cannot:

| Inline it | Keep the name |
| --- | --- |
| A self-describing literal or expression (`v.union(...)` of obvious literals, `x.fields`) | A magic value the name explains (`MAX_FILE_BYTES`, `SCORE_FLOOR`) |
| A one-line alias for an expression used once | A lookup table hoisted out of a render so it is not rebuilt per render |
| A helper whose body reads the same at the call site | A sub-component, which is single-use by nature |
| A type alias standing in for one short inline type | A return type that keeps a signature readable |

The bar is the project's own philosophy: zero unnecessary abstractions.
When in doubt, don't.

## Pass 4: comment audit

Comments come after the structural passes for a reason: the supply is
unbounded and each kill feels like progress, so an audit run first eats the
whole budget and leaves the expensive findings undiscovered. Make one pass per
file. If you are on your third read of the same comments, you are done, and
whatever is left is not what is wrong with this change.

**The sweep never adds comments. Not one, not ever, no exceptions.** This pass
removes comments; it is not an opportunity to document the change, even when a
fix from an earlier pass leaves behind something a future reader could
misread. If you catch yourself about to write a new one mid-sweep, that is the
same signal as finding a bug: stop, and do not add it. Flag it to the user
instead, by file and line, with the exact fact it would carry, and let them
decide whether it belongs in the code, in the commit message, or nowhere. "I
checked it against the keep bar and it earns its place" is not a license to
add it yourself — you are both the author and the judge of that claim, which
is exactly why it needs a second opinion before it lands, not a self-review.
A sweep that ends with more comment lines than it started with has inverted
its own purpose: the reviewer now has more to read because you polished it.
The same standard runs backwards into the change itself. A diff whose new
code is half comment does not need a stricter audit here, it needed fewer
comments written in the first place, and the fix is to delete them rather
than to justify each one.

Read every comment in the swept files, every one and not only the comments
this change wrote, and apply the test: **does this state something the code
cannot say?** Keep it only if yes. A four-line header that predates your work
but narrates the helpers below it is exactly the kind of noise a reviewer
shouldn't have to wade through; trim it to the one clause that carries a real
constraint (a gotcha, a why, an invariant) and drop the rest, relocating that
surviving clause to where it belongs if the header sat far from it.

| Kill | Keep |
| --- | --- |
| Narrates control flow ("fall through to the finally block") | A constraint invisible in code ("first() not unique(): legacy rows may share a sid") |
| Restates the name of the thing below it ("// verify the phone" above `verifyPhone(...)`) | A deliberate tradeoff ("a rare double-count beats a leak") |
| Summarizes what the function below does, even loosely ("reads recent insights, plans, embeds, persists" over a function that does exactly that) | A policy decision ("inbound is free, no billing here") |
| Announces a mechanism the signature already shows ("Bounded, resumable drain over pending rows" over `drain(limit)`) | Why the obvious alternative is wrong ("Twilio does not retry, so always respond 200") |
| Compares to code that no longer exists ("one query instead of the seven reads we used to make") | A guarantee callers rely on ("scheduled iff this transaction commits") |
| Explains what the next line does | A constraint that spans files ("1536 must match the vector column and the RPC") |
| Talks to the PR reviewer ("this is now safe because...") | Distinguishes near-identical siblings only when the names cannot ("cosine, 1 = identical" on a `number` return) |
| Justifies a visible construct with its textbook benefit ("derived from the canonical list so the two can't drift" above `...CANONICAL.map(...)`) | A non-obvious consequence of the construct ("adding a platform here also makes it importable; that is intentional") |

The "compares to code that no longer exists" row deserves emphasis: it is
the easiest one to write during a refactor and the most useless one a month
later. If a comment only makes sense to someone who saw the old version,
delete it.

**Rationale-shaped restatement is still restatement.** A comment can dress
up the obvious as a "why": "derived from X so they can't drift" above a
derivation from X, "extracted to avoid duplication" above the extracted
helper, "checked before writing so bad rows never land" above an early
return. These read as rationale but state only the textbook benefit every
reader already attributes to the pattern on sight. The test: would a
competent reader, seeing the code alone, ever ask "why is this here?" If
nobody would ask, the answer is noise; delete it. Keep the why only when
the reader's guess would be wrong, when a real alternative was rejected for
a reason the code can't show, or when the consequence is non-obvious. The
same applies to a comment that duplicates an adjacent user-facing string:
an error message or UI label that already explains the situation needs no
comment restating it.

**Reach for a rename before reaching for a comment.** A comment that exists to
explain what a name means is a naming bug wearing a disguise. `strandedRows`
needing "rows marked Answered with no transcript" above it should have been
`answeredWithoutTranscript`, and then there is nothing left to write. Before
keeping any comment, ask whether renaming the thing it sits on, splitting a
function, or extracting a well-named local would carry the same fact in the
code. If it would, do that and delete the comment. This applies to survivors
you inherited, not just to comments the change wrote.

**Default to kill; make each survivor earn it.** For every comment, name in
a few words the specific fact it carries that the code cannot: the
constraint, the tradeoff, the guarantee, the cross-file coupling, the reason
the obvious path is wrong. If you cannot name one, delete the comment.
"It orients the reader" and "it summarizes the function" are not facts. The
verdict on each comment is KILL, KEEP, or TRIM, never "leave it, it's
harmless": a harmless comment is still noise the reviewer must read.

**Function and file headers are where restatement hides.** A header that
paraphrases the body's steps narrates control flow just as much as an inline
`// loop over items`; the signature plus a glance at the body already say
it. Kill it unless it states a contract the body does not: idempotent, fails
open, who may call it, an ordering guarantee, an invariant callers depend on.
A file header that restates the file's own path ("Campaign-specific business
rule constants" atop `campaigns/constants.ts`) is the same failure.

**TRIM is a first-class action, not a fallback to KEEP.** Comments often
bury one load-bearing clause inside restatement, e.g. "Bounded, resumable
drain over pending rows. A failure marks the row failed and does not abort
the batch." Only the second sentence earns its place. Cut to the surviving
clause and delete the rest; a three-line header that reduces to one clause
becomes one line. Do not keep the scaffolding for the sake of the clause.

Docstrings on exported functions follow the same test. A docstring that
paraphrases the signature is noise; one that states the contract (fails
open, idempotent, atomic, who may call it) earns its place.

Two more rules for survivors:

- **Self-contained.** A comment purely explains the code. Never point the
  reader at anything external (a ticket, a PR, a doc); the comment itself
  must suffice. The one exception is pass 2's deletion marker on
  intentionally kept dead code.
- **Brief.** One or two lines. A comment that needs a paragraph is usually
  explaining code that should be simpler.

## Pass 5: humanizer on survivors

Run the [[humanizer]] pass over every comment you kept. In practice the
patterns that show up in code comments are:

- Em dashes. Replace with a comma, a colon, or two sentences.
- AI vocabulary: "crucial", "leverage", "seamlessly", "robust", "ensure".
- Rule-of-three enumerations that don't correspond to three real things.
- Inflated significance ("this critical helper...").

Comments should read like a teammate wrote them in passing, not like
documentation prose.

## Pass 6: file placement

Check each new file sits where the codebase's existing layout says it
should: same domain-module conventions, same naming pattern, same
runtime-directive rules as its siblings. If a file is misplaced, move it
and fix imports; if placement is defensible either way, leave it and say
nothing.

## Pass 7: prove nothing broke

Re-run every gate the project has, in full, after the sweep:

- Typecheck
- Lint (at minimum the swept files)
- The complete test suite, not a subset

All green is the exit criterion. Deletions are behavior-neutral, so a red gate
on a "harmless" cleanup means you broke something: fix it or revert that edit.
A replacement from pass 1 is different, and the tests are how you check it: if
a test changes color, you have found one of the edge-case deltas that pass 1
told you to enumerate. Decide whether the new behavior or the test is right,
then report it either way.

One red is worth naming because it looks like an edge-case delta and is not. An
extraction can move a seam that a test was asserting across, so the test fails
while the behavior is identical: it mocked the module the code used to live in,
and the logic now lives one module over. The fix is to re-point the test at the
new boundary, usually by mocking one level lower so the extracted code runs for
real. Do not relocate the assertion to a thinner one, and do not let the
guarantee it was pinning quietly become nobody's job. If you cannot keep the
guarantee covered where it was, the extraction costs more than the duplication
did: revert it and report why.

Green gates prove the sweep was safe. They do not prove it was thorough.

## If you find a bug

You will sometimes spot a real defect while sweeping. Do not fix it
silently inside the sweep. Flag it to the user with file and line, and let
them decide whether it joins this change or becomes its own. Mixing a
behavior fix into a polish pass destroys the reviewer's trust that the
sweep diff is safe to skim.

A pre-existing defect is not the same as an edge-case delta introduced by a
pass 1 replacement, and not the same as a semantic slip the change itself
introduced that pass 2 caught. Flag the first, report the second, fix the
third.

Pass 4 runs on the same rule: a comment you want to write is a claim about
the code, and a sweep does not get to unilaterally decide its own claims are
correct. Flag it exactly like a bug, and let it land somewhere only if the
user says so.

## Commits

Match the repository's existing commit convention and the user's standing
instructions (per-file commits, branch naming, no AI attribution).
Cleanup commits are `chore(...)` unless a real extraction or replacement
happened, which is `refactor(...)`. Messages say why, not what: "drop comments
that narrate control flow" beats "update comments".

## Report

End with a short summary in prose, grouped by pass, so the user can skim it
instead of the diff. Cover what was replaced and what it now calls, what was
deleted, what was kept and why, what the gates said, and anything flagged but
deliberately not touched.

Three things the report must include that are easy to omit:

- **What you searched and ruled out.** Pass 1's enumeration, with its verdict
  per item, including the ones that came back KEEP. A sweep that hunted hard
  and found nothing must not look identical to one that never hunted.
- **Every edge-case delta** a replacement introduced, input by input.
- **The comment count, before and after.** One line: how many comments the
  swept files carried when you started, how many they carry now. It must
  never go up. If it did, that is a rule violation to name and undo, not a
  footnote to justify, and any comment you wanted to add but didn't goes in
  the report as a flagged suggestion instead, the same as a found bug.

If the entire report is comment trims, say that plainly rather than presenting
it as a finished sweep. Sometimes it is the honest answer on a small diff. More
often it means passes 1 and 2 were skimmed, and the reviewer is about to find
what you missed.

## Related skills

- [[humanizer]] — the pattern list for pass 5.
