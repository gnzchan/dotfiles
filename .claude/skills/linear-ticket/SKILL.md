---
name: linear-ticket
version: 1.0.0
description: |
  Create, update, or comment on Linear tickets for Christian (DealFuel /
  ReplySmart). Covers workspace defaults, assignee, label taxonomy, voice,
  structure, and the humanizer pass. Use whenever the user asks to file,
  update, or comment on a Linear ticket via the Linear MCP.
allowed-tools:
  - mcp__claude_ai_Linear__save_issue
  - mcp__claude_ai_Linear__save_comment
  - mcp__claude_ai_Linear__list_issues
  - mcp__claude_ai_Linear__list_projects
  - mcp__claude_ai_Linear__list_teams
  - mcp__claude_ai_Linear__list_issue_labels
  - mcp__claude_ai_Linear__list_users
  - mcp__claude_ai_Linear__get_issue
  - mcp__claude_ai_Linear__list_comments
---

# Linear ticket

When Christian asks to create, update, or comment on a Linear issue, follow
the rules below. Apply all of them by default. No need to be reminded.

## Workspace defaults

- **Workspace:** DealFuel
- **Team:** `Development` (issue prefix `DEV`, e.g. `DEV-225`)
- **Project:** `ReplySmart` (Christian's main project)
- **Other projects** in the workspace: DealFuel, DealFloor, HQ, Authomatic
- **MCP:** connected via `claude mcp add --transport http linear --scope user https://mcp.linear.app/mcp`

When the user doesn't specify, assume `team: "Development"` and
`project: "ReplySmart"`.

## Assignee

Always set `assignee: "Chan Gonzales"` (the display name).

Do not use the email `christian@dealfuel.ai`. The email form silently drops
the assignment when combined with the `project` field. The display name
resolves cleanly. This was confirmed twice in the same session (DEV-221,
DEV-222 both lost their assignment).

## Labels

Pick by source and commitment:

- **Bug** — something is broken in shipped product.
- **Feature** — new feature the team has committed to build (internal
  initiative or roadmap item).
- **Feature request** — a customer or external stakeholder asked for
  something we haven't committed to. Default to this when filing a ticket
  prompted by a customer or support handoff.
- **decision-needed** — add when the ticket has an open question that
  needs alignment before work can start. Pair with the primary label.

If you can't tell whether something is `Feature` or `Feature request`,
ask before picking. Don't guess.

Why the split: Christian separated `Feature` from `Feature request` so the
backlog clearly distinguishes what the team plans to build from what
customers are asking us to consider. Mixing them muddies prioritization.

## Voice and structure

1. **First-person voice.** Write as if Christian is creating the ticket.
   Use "I found", "I noticed", "I want to". Never sound like an external
   observer. No "the user", no "the agent reports", no "Claude".
2. **Short descriptions.** 3-5 lines max in the description. Just enough
   context to know what the issue is. Findings, progress, debate, and
   updates all go in **comments**, not the description.
3. **No em dashes.** Use commas, parentheses, periods, or split into two
   sentences. Em dashes are an immediate AI-writing tell.
4. **No AI vocabulary.** Avoid "crucial", "pivotal", "showcase", "key",
   "landscape", "testament", "underscore", "vibrant", "leverage", etc.
   See the [[humanizer]] skill for the full list.
5. **Specific beats vague.** "Drop-off on first signup screen" beats
   "may impact user experience". Concrete numbers, file paths, and
   verbatim error messages where they exist.
6. **Comments for next steps.** When findings need verification, a repro
   from a customer, or a product decision, drop a comment on the ticket
   spelling that out. Don't bury it in the description.

## Cross-references inside Linear

Inside Linear bodies (issue descriptions and comments cross-referencing
other issues), reference issues by ID (e.g. `DEV-225`), not title.

## Talking about tickets in conversation

When mentioning a ticket back to Christian in conversation, always pair
the ID with a short context line. Never say "DEV-206" alone. Say
"DEV-206 (verify segment delete cleanup)" or similar. He doesn't want to
keep cross-checking Linear to remember what each ticket is about.

## Status lifecycle

Backlog → Todo → In Progress → In Review → Done.

For self-driven ReplySmart work, Christian moves status. Don't auto-set
status unless asked.

## Process

For every ticket create, update, or comment:

1. **Draft** the title, description, and any comment body following the
   voice and structure rules above.
2. **Humanizer pass.** Before submitting, mentally run the [[humanizer]]
   skill over the text. Strip em dashes, AI vocabulary, rule of three,
   inflated significance, vague attributions, promotional adjectives,
   and conjunctive padding.
3. **Verify required fields:**
   - `title`
   - `team: "Development"` (when creating)
   - `project: "ReplySmart"` (unless user specifies otherwise)
   - `assignee: "Chan Gonzales"` (unless user specifies otherwise)
   - `labels: [...]` (at least one primary: Bug, Feature, or Feature request)
4. **Submit** via `mcp__claude_ai_Linear__save_issue` or
   `mcp__claude_ai_Linear__save_comment`.
5. **Report back** with the ID and a short context line:
   "Created DEV-XX (short description). https://linear.app/dealfuel/issue/DEV-XX/..."

## Workflow for assigned work (non-ReplySmart)

When CTO drops an issue ID in `#development-linear` Slack channel:

1. Pull context via Linear MCP (`get_issue`, `list_comments`).
2. When done: comment on the issue summarizing what shipped, move to
   In Review, open the PR, ping CTO on Slack.

## Examples

### Good description

> ## Problem
> On signup, Clerk showed a red error: "Password has been found in an
> online data breach." This forced a password change at the worst moment
> and likely caused drop-off.
>
> ## Cause
> Clerk's HIBP "Reject compromised passwords" setting was on. The error
> came from `signUp.create()` and rendered through the existing error
> banner. No code issue.
>
> ## Fix
> Toggled "Reject compromised passwords" off in Clerk Dashboard. No code
> changes needed.

### Bad description (would fail a humanizer pass)

> The signup flow is currently impacted by a critical issue — users are
> being blocked by Clerk's password breach validation, which serves as
> a key friction point in the onboarding funnel and underscores the
> importance of reviewing our authentication settings — see attached
> screenshots for the full context.

Issues: em dashes, "critical issue", "key friction point",
"underscores the importance", "currently impacted by", wall of one
paragraph instead of structured sections.

## Related skills

- [[humanizer]] — full AI-writing pattern list. Run before posting any
  ticket if the draft still reads like AI prose.
