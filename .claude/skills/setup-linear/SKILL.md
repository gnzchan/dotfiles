---
name: setup-linear
description: |
  Use when a repo needs its own Linear connection: first-time Linear setup in
  a project, pointing a project at a different Linear account or workspace, or
  when linear-ticket reports there is no .claude/linear.json binding. Adds a
  per-project Linear MCP server, walks through OAuth, and writes the binding
  that linear-ticket reads.
---

# Setup Linear

Bind the current repo to one Linear workspace. Two separate things happen, and
both are needed:

1. **Connection (auth)** — a per-project MCP server, authorized to the target
   Linear account. This is what actually grants read/write.
2. **Binding (defaults)** — `.claude/linear.json`, naming the server plus the
   workspace/team/assignee defaults. This is what `linear-ticket` reads.

A skill can't authenticate anything, so step 3 below (OAuth) is the user's to
do in a browser. Everything else you run.

## Steps

### 1. Gather the details

Ask the user (skip anything you already know):

- **Which Linear account/workspace** this repo should use.
- **Server name** — propose `linear-<short-slug>` (e.g. `linear-gk`). Lowercase,
  hyphens. This becomes the tool namespace `mcp__<server>__*`.
- **Team name** and its **issue prefix** (e.g. `GK Marketing Services` / `GK`).
- **Assignee** display name for the default assignee.
- **Default project**, or none.

You can confirm team/assignee against Linear in step 4 instead of guessing.

### 2. Add the MCP server

Run, substituting the chosen name:

```bash
claude mcp add --transport http <server> --scope local https://mcp.linear.app/mcp
```

Use `--scope local` (default): the credential stays in your private per-project
config, never committed. The binding file carries the non-secret defaults and
*is* committed, so teammates inherit "this repo uses workspace X" and authorize
their own account.

### 3. Authenticate (user does this)

Tell the user to run `/mcp`, pick the new `<server>`, and complete the browser
login **as the target Linear account**. Logging into the wrong account binds
the wrong workspace. Wait for them to confirm the server shows connected.

### 4. Verify the connection

Call `mcp__<server>__list_teams`. Confirm the expected team/workspace appears.
If it shows a different workspace, the user authorized the wrong account: have
them disconnect that server in `/mcp` and redo step 3.

Then call `mcp__<server>__list_users` to get the assignee's exact display name.
Assign by display name, never email (email drops the assignment when combined
with a project).

### 5. Write the binding

Write `.claude/linear.json` at the repo root:

```json
{
  "server": "linear-gk",
  "workspace": "GK Marketing Services",
  "team": "GK Marketing Services",
  "teamKey": "GK",
  "assignee": "Christian Gonzales",
  "project": null
}
```

- `project: null` when the workspace has no project yet.
- Add an optional `"labels"` object only if this workspace's label names differ
  from linear-ticket's defaults (Bug / Feature / Feature request /
  decision-needed).
- No tokens or secrets in this file. It is committed.

### 6. Confirm

Tell the user `/linear-ticket` now files into this workspace whenever they work
in this repo, and no other repo is affected.

## Notes

- **One server per account, not per repo-that-shares-an-account.** If two repos
  use the same Linear account, each still needs its own local server + OAuth
  (local scope is per-project). That re-auth is the cost of per-project
  isolation.
- **Switching a repo to a different workspace:** disconnect the old server in
  `/mcp`, then run this skill again with the new details.
- **Removing the old global connection:** if migrating off the account-global
  claude.ai Linear connector, do it only after this setup is verified end to
  end, so you are never left with no Linear access.

## Related skills

- [[linear-ticket]] — reads `.claude/linear.json` and files tickets.
