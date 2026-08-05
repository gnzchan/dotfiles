---
name: linear-ticket
description: |
  Use when the user asks to create, update, or comment on a Linear ticket
  via the Linear MCP. Workspace, team, and assignee come from the repo's own
  binding (.claude/linear.json), so this works across DealFuel, GK Marketing,
  and any other workspace. Covers label taxonomy, voice, structure, and the
  humanizer pass.
---

# Linear ticket

When Christian asks to create, update, or comment on a Linear issue, follow
the rules below. Apply all of them by default. No need to be reminded.

## Read the repo binding first

Every project points at its own Linear workspace. Read `.claude/linear.json`
at the repo root before doing anything. It defines:

- `server` — the MCP server name for this repo. Call Linear through its tools:
  `mcp__<server>__save_issue`, `mcp__<server>__list_teams`, etc. Never assume
  a server; use the one named here.
- `workspace` — display name, for talking to the user.
- `team` — team name (pass as `team` when creating).
- `teamKey` — issue prefix (e.g. `GK`, `DEV`).
- `assignee` — display name to assign by default.
- `project` — default project, or `null` if the workspace has none yet.
- `labels` — optional per-workspace label names, if they differ from the
  defaults below.

**No `.claude/linear.json` in this repo?** Stop and tell the user to run
`/setup-linear` first. Do not fall back to another workspace or guess.

## Assignee

Assign by the `assignee` display name from the binding.

Never assign by email. The email form silently drops the assignment when
combined with the `project` field; the display name resolves cleanly.
(Confirmed twice: DEV-221, DEV-222 both lost their assignment.)

## Labels

Pick by source and commitment:

- **Bug** — something is broken in shipped product.
- **Feature** — new feature the team has committed to build (internal
  initiative or roadmap item).
- **Feature request** — a customer or external stakeholder asked for something
  we haven't committed to. Default to this when filing a ticket prompted by a
  customer or support handoff.
- **decision-needed** — add when the ticket has an open question that needs
  alignment before work can start. Pair with the primary label.

If you can't tell whether something is `Feature` or `Feature request`, ask
before picking. Don't guess. The split keeps what the team plans to build
separate from what customers are asking us to consider; mixing them muddies
prioritization.

## Voice and structure

1. **First-person voice.** Write as if Christian is creating the ticket. Use
   "I found", "I noticed", "I want to". Never "the user", "the agent reports",
   or "Claude".
2. **Short descriptions.** 3-5 lines max. Just enough context to know what the
   issue is. Findings, progress, debate, and updates go in **comments**.
3. **No em dashes.** Use commas, parentheses, periods, or two sentences.
4. **No AI vocabulary.** Avoid "crucial", "pivotal", "showcase", "key",
   "landscape", "testament", "underscore", "vibrant", "leverage". See the
   [[humanizer]] skill for the full list.
5. **Specific beats vague.** "Drop-off on first signup screen" beats "may
   impact user experience". Concrete numbers, file paths, verbatim errors.
6. **Comments for next steps.** When findings need verification, a customer
   repro, or a product decision, drop a comment spelling that out. Don't bury
   it in the description.

## Cross-references inside Linear

Inside Linear bodies, reference other issues by ID (e.g. `GK-12`), not title.

## Talking about tickets in conversation

Always pair the ID with a short context line. Never "GK-12" alone. Say
"GK-12 (fix hour-chart timezone)". Christian shouldn't have to cross-check
Linear to remember what a ticket is about.

## Status lifecycle

Backlog → Todo → In Progress → In Review → Done. Don't auto-set status unless
asked.

## Process

For every create, update, or comment:

1. **Read the binding** (`.claude/linear.json`). No binding → `/setup-linear`.
2. **Draft** title, description, and any comment following the voice rules.
3. **Humanizer pass.** Run the [[humanizer]] skill over the text. Strip em
   dashes, AI vocabulary, rule of three, inflated significance, vague
   attributions, promotional adjectives, conjunctive padding.
4. **Verify required fields** (when creating):
   - `title`
   - `team` = binding `team`
   - `project` = binding `project` (omit if `null`)
   - `assignee` = binding `assignee` (unless the user says otherwise)
   - `labels` = at least one primary (Bug, Feature, or Feature request)
5. **Submit** via `mcp__<server>__save_issue` / `mcp__<server>__save_comment`.
6. **Report back** with the ID, a short context line, and the URL returned by
   the call: "Created GK-XX (short description). <url>".

## Examples

### Good description

> ## Problem
> On signup, Clerk showed a red error: "Password has been found in an online
> data breach." This forced a password change at the worst moment and likely
> caused drop-off.
>
> ## Cause
> Clerk's HIBP "Reject compromised passwords" setting was on. The error came
> from `signUp.create()` and rendered through the existing error banner. No
> code issue.
>
> ## Fix
> Toggled "Reject compromised passwords" off in Clerk Dashboard. No code
> changes needed.

### Bad description (fails a humanizer pass)

> The signup flow is currently impacted by a critical issue — users are being
> blocked by Clerk's password breach validation, which serves as a key
> friction point in the onboarding funnel and underscores the importance of
> reviewing our authentication settings — see attached screenshots.

Issues: em dashes, "critical issue", "key friction point", "underscores the
importance", "currently impacted by", one wall of text instead of sections.

## Related skills

- [[setup-linear]] — bind a repo to its Linear workspace (creates the binding
  this skill reads).
- [[humanizer]] — full AI-writing pattern list. Run before posting any ticket.
