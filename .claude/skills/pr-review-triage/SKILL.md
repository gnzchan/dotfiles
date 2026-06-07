---
name: pr-review-triage
version: 1.0.0
description: |
  Triage a PR code review into fix, already-done, and skip buckets, then produce
  concise one-sentence bullets for the skipped items. Output is ready to paste
  as a PR comment so reviewers see that skipped findings are deliberate, not
  oversights. Use when addressing a PR review (bot or human) and you want to
  document intentional non-fixes cleanly.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
---

# PR Review Triage

Given a PR code review, silently split each finding into Fix, Already done,
and Skip. Then emit **only the postable skip bullets** — ready to paste as
a PR comment. No audit section, no categorization labels, no commentary.

## Step 1: audit silently

Before producing output, verify every finding against the actual code.
Bots hallucinate. Reviewers often re-raise issues already addressed in
a later commit. For each finding, check:

- Does the cited file:line actually have the issue described?
- Is there a later commit on the branch that already addresses it?
- Does the suggestion assume an older API, different lint rules, or a
  different codebase convention than the one in use here?

Useful checks:
- `git log --oneline <base>..HEAD -- <path>`
- `git show HEAD:<path>`
- `Grep` for the claimed pattern

**Skip the audit entirely in the output.** Keep classification private.
Only emit the final bullets.

## Writing Skip bullets

For each skipped finding, produce one line in this shape:

```
- **<short label>**: <one-sentence reason>.
```

### Rules

- **One sentence.** Cut anything that isn't the core reason.
- **No em dashes.** Use commas, periods, or line breaks.
- **No AI vocabulary.** Avoid "crucial", "pivotal", "showcase", "key",
  "landscape", "testament", "underscore", "vibrant", and similar.
- **Specific beats vague.** "We won't hit this at current volume" is
  better than "not needed right now".
- **Direct.** Start with what was skipped, follow with the why.
- **Concrete reasoning.** Cite the actual constraint (existing cap,
  scope, cost, convention) rather than hand-waving.

### Examples

- **Max range cap**: The 10k result cap already bounds cost, a window cap on top would be redundant.
- **`meta.truncated` flag**: For a case the dashboard won't hit at current volume.
- **Hash-before-timingSafeEqual**: Overkill for an internal endpoint with one known caller.
- **Playwright smoke test**: Out of scope for v1, README already has a curl example.
- **`ctx.db.get` flagged as critical**: False alarm. This repo's lint rule requires the two-arg form.
- **Trailing newline on `.env.template`**: Pre-existing across multiple entries, not introduced by this PR.

## Output shape

Only the skip bullets. Nothing else.

- Flat markdown list
- Optional one-line intro like "Running through what we skipped and why:"
- No categorization, no headings, no commentary between items
- No audit section — the triage happens in your head, not on screen

The output should be directly pasteable as a PR comment with zero editing.

## Related skills

If the bullets still read like AI prose (promotional language, inflated
stakes, rule of three, em dashes), invoke the `humanizer` skill to
tighten them further.
