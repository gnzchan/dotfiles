---
name: final-sweep
description: |
  One last polish pass over a finished change before it ships: audit every
  comment against a keep/kill test, run humanizer on the survivors, delete
  dead and orphaned code, collapse real repetition, sanity-check file
  placement, then re-run every quality gate to prove the sweep changed
  nothing behavioral. Use when the user says "final sweep", "polish this up",
  "clean up before commit", "remove useless comments", "check for dead code",
  "one last pass", or when implementation work is done and the diff is about
  to be reviewed, committed, or PR'd. Not a bug hunt and not a refactor pass;
  it never changes behavior, only removes noise.
---

# Final sweep

You are doing the last pass over a completed change before someone else
reads it. The code already works and the tests already pass. Your job is to
remove everything that will waste the reviewer's attention, and nothing
else. A sweep that changes behavior has failed.

## Scope

Sweep only the files the change touched. Get the list from git, not from
memory:

```bash
git diff --name-only <base-branch>...HEAD
git status --short   # plus anything uncommitted
```

Do not wander into untouched files, even when they have the same problems.
Pre-existing mess is a separate task the user has to ask for. One
exception: a one-line fix directly adjacent to your change (an unused
import your edit exposed, for example) is fine. Note it when reporting.

## Pass 1: comment audit

Read every comment in the swept files and apply the test: **does this state
something the code cannot say?** Keep it only if yes.

| Kill | Keep |
| --- | --- |
| Narrates control flow ("fall through to the finally block") | A constraint invisible in code ("first() not unique(): legacy rows may share a sid") |
| Restates the name of the thing below it ("// verify the phone" above `verifyPhone(...)`) | A deliberate tradeoff ("a rare double-count beats a leak") |
| Summarizes what the function below does, even loosely ("reads recent insights, plans, embeds, persists" over a function that does exactly that) | A policy decision ("inbound is free, no billing here") |
| Announces a mechanism the signature already shows ("Bounded, resumable drain over pending rows" over `drain(limit)`) | Why the obvious alternative is wrong ("Twilio does not retry, so always respond 200") |
| Compares to code that no longer exists ("one query instead of the seven reads we used to make") | A guarantee callers rely on ("scheduled iff this transaction commits") |
| Explains what the next line does | A constraint that spans files ("1536 must match the vector column and the RPC") |
| Talks to the PR reviewer ("this is now safe because...") | Distinguishes near-identical siblings only when the names cannot ("cosine, 1 = identical" on a `number` return) |

The "compares to code that no longer exists" row deserves emphasis: it is
the easiest one to write during a refactor and the most useless one a month
later. If a comment only makes sense to someone who saw the old version,
delete it.

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
  must suffice. The one exception is pass 3's deletion marker on
  intentionally kept dead code.
- **Brief.** One or two lines. A comment that needs a paragraph is usually
  explaining code that should be simpler.

## Pass 2: humanizer on survivors

Run the [[humanizer]] pass over every comment you kept. In practice the
patterns that show up in code comments are:

- Em dashes. Replace with a comma, a colon, or two sentences.
- AI vocabulary: "crucial", "leverage", "seamlessly", "robust", "ensure".
- Rule-of-three enumerations that don't correspond to three real things.
- Inflated significance ("this critical helper...").

Comments should read like a teammate wrote them in passing, not like
documentation prose.

## Pass 3: dead, orphaned, and vestigial code

The change deleted, renamed, or reshaped things. Find what it stranded.
This is line-by-line, not grep-and-move-on: read every symbol the swept
files touch and ask "does this still do work?" A symbol that compiles and
is referenced can still be dead — the linter and a name grep both say
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
   Flag it when:
   - it is now only ever set to one constant value (the code that varied it
     is gone), so every branch reading it is effectively decided at compile
     time — delete the field and collapse the branch;
   - it is written but never read, or read but the read can no longer change
     any outcome;
   - it is a struct/interface field or function arg that every call site now
     passes the same literal for, or that nothing passes at all.
   Example: a `PhoneFilterConfig { excludeTypes, excludeEmpty }` where the
   UI that toggled `excludeEmpty` was replaced, so it is pinned to `false`
   everywhere and its `if (excludeEmpty …)` branch is unreachable. Grep
   shows it referenced in five places; it is still dead. Remove the field,
   the branch, and the now-constant literals.
6. **Reachability.** For each conditional the change touched, ask whether
   any real input can still take each side. A branch guarding on a
   now-constant flag, an `else` for a case the new types forbid, or an
   option in a menu nothing can select is dead — remove it.

If something is intentionally kept despite being unreferenced or inert (a
deploy shim for pending scheduled jobs, a migration), it must have a comment
saying when it can be deleted and, when a tracker exists, the ticket ID.

## Pass 4: DRY, with restraint

Look for repetition inside the swept files. Extract only when it is real:

- Three or more occurrences of the same multi-line block: extract a small
  local helper.
- Two occurrences: usually leave it. Two call sites reading plainly beat
  one abstraction plus indirection.
- Never extract across module boundaries just to deduplicate. Shared code
  needs a shared reason, not just shared shape.

The bar is the project's own philosophy: zero unnecessary abstractions.
When in doubt, don't.

## Pass 5: file placement

Check each new file sits where the codebase's existing layout says it
should: same domain-module conventions, same naming pattern, same
runtime-directive rules as its siblings. If a file is misplaced, move it
and fix imports; if placement is defensible either way, leave it and say
nothing.

## Pass 6: prove nothing broke

Re-run every gate the project has, in full, after the sweep:

- Typecheck
- Lint (at minimum the swept files)
- The complete test suite, not a subset

All green is the exit criterion. A sweep is behavior-neutral by
definition, so any red gate means you broke something with a "harmless"
cleanup: fix it or revert that edit.

## If you find a bug

You will sometimes spot a real defect while sweeping. Do not fix it
silently inside the sweep. Flag it to the user with file and line, and let
them decide whether it joins this change or becomes its own. Mixing a
behavior fix into a polish pass destroys the reviewer's trust that the
sweep diff is safe to skim.

## Commits

Match the repository's existing commit convention and the user's standing
instructions (per-file commits, branch naming, no AI attribution).
Cleanup commits are `chore(...)` unless a real extraction happened, which
is `refactor(...)`. Messages say why, not what: "drop comments that narrate
control flow" beats "update comments".

## Report

End with a short summary in prose, grouped by pass: what was removed and
why, what was kept and why, what was extracted, what the gates said, and
anything flagged but deliberately not touched. The user should be able to
skim the summary instead of the diff.

## Related skills

- [[humanizer]] — the pattern list for pass 2.
