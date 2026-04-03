---
allowed-tools: Bash, Read, Write, Edit, Glob, AskUserQuestion
description: Pull all DealFuel tooling (skills, rules, hooks, mcp, validate) from shank-labs/claude-tooling into this project
---

# Setup DealFuel Project

Pull all shared tooling from the `shank-labs/claude-tooling` repo into the current project. This includes skills, config files, validation, and rules.

## Source repo: `shank-labs/claude-tooling` (branch: `main`)

## Process

### 1. Skills

- List all skills: `gh api repos/shank-labs/claude-tooling/contents/.claude/skills --jq '.[].name'`
- For each skill:
  - Create `.claude/skills/{skill-name}/`
  - Fetch SKILL.md: `gh api repos/shank-labs/claude-tooling/contents/.claude/skills/{skill-name}/SKILL.md -H "Accept: application/vnd.github.raw+json"` and write it
  - Check for references: `gh api repos/shank-labs/claude-tooling/contents/.claude/skills/{skill-name}/references --jq '.[].name'` — if found, fetch each file and write to `references/`

### 2. CLAUDE.md

- Fetch: `gh api repos/shank-labs/claude-tooling/contents/CLAUDE.md -H "Accept: application/vnd.github.raw+json"`
- If the project already has a `CLAUDE.md`:
  - Read the existing one
  - Append the fetched content under a `# DealFuel Shared Guidelines` heading, preserving everything that was already there
  - Do NOT duplicate content — if a `# DealFuel Shared Guidelines` section already exists, replace that section only
- If no `CLAUDE.md` exists, write it directly

### 3. RULES.md

- Fetch: `gh api repos/shank-labs/claude-tooling/contents/RULES.md -H "Accept: application/vnd.github.raw+json"`
- Write to `RULES.md` in project root (overwrite — this is a shared standard)

### 4. validate.ts

- Fetch: `gh api repos/shank-labs/claude-tooling/contents/validate.ts -H "Accept: application/vnd.github.raw+json"`
- Write to `validate.ts` in project root (overwrite — this is a shared standard)

### 5. .mcp.json

- Fetch: `gh api repos/shank-labs/claude-tooling/contents/.mcp.json -H "Accept: application/vnd.github.raw+json"`
- If the project already has `.mcp.json`:
  - Read existing, parse both as JSON
  - Merge: add any new `mcpServers` entries from the repo without removing existing ones
  - If a server with the same key already exists, keep the project's version
  - Write back the merged result
- If no `.mcp.json` exists, write it directly

### 6. hooks.json

- Fetch: `gh api repos/shank-labs/claude-tooling/contents/.claude/hooks.json -H "Accept: application/vnd.github.raw+json"`
- If the project already has `.claude/hooks.json`:
  - Read existing, parse both as JSON
  - For each hook type (e.g. `SessionStart`, `PreToolUse`): merge the arrays — append new hook entries that don't already exist (compare by `command` field to detect duplicates)
  - Write back the merged result
- If no `.claude/hooks.json` exists, write it directly

### 7. settings.json

- Fetch: `gh api repos/shank-labs/claude-tooling/contents/.claude/settings.json -H "Accept: application/vnd.github.raw+json"`
- If the project already has `.claude/settings.json`:
  - Read existing, parse both as JSON
  - Merge: add new keys/entries without overwriting existing ones
  - For nested objects like `enabledPlugins`, merge the entries
  - Write back the merged result
- If no `.claude/settings.json` exists, write it directly

### 8. Summary

List everything that was installed or merged.

## Important
- If `gh` auth fails, tell the user to run `gh auth login`
- Only fetch from the `main` branch
- When merging JSON files, always preserve valid JSON formatting
- If {{ARGS}} is provided with value `skills-only`, only run step 1
