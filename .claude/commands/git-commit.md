---
allowed-tools: Bash(git:*)
description: Create a git commit following CLAUDE.md guidelines
---

# Git Commit Command

You are tasked with creating a git commit that follows the strict guidelines defined in CLAUDE.md. Follow these steps:

## Pre-Commit Analysis

1. **Check staged changes**: Run `git status` and `git diff --staged` to see what files are currently staged
2. **Review recent commits**: Run `git log --oneline -10` to understand the repository's commit message style
3. **Analyze the changes**: Understand what the staged changes actually do (not just what the user requested)

## Commit Message Guidelines

### Style Detection
- **Match repository format**: Follow the dominant commit message style found in recent commits
- **Common patterns to look for**:
  - Conventional Commits: `feat:`, `fix:`, `docs:`, `refactor:`, etc.
  - Imperative mood: `Add validation logic`, `Fix crash on startup`
  - Other patterns: Capitalization, punctuation, length preferences

### Message Content
- **Base on actual changes**: Write the message based on the staged diff, not user instructions
- **Focus on "why"**: Explain the purpose, not just what was changed
- **Emphasize simplicity**: Highlight when changes make code simpler, more elegant, or more reliable
- **Be concise**: 1-2 sentences maximum
- **Use descriptive language**: `Add validation logic` not `Add stuff`

## Attribution Rules

**NEVER include**:
- AI authorship mentions
- Co-author tags referencing Claude Code or AI tools
- Any indication that the commit was AI-generated

## Execution Process

1. **Analyze staged changes** using git commands
2. **Draft commit message** based on:
   - Repository's existing commit style
   - Actual staged changes (not user request)
   - Purpose and context of the changes
3. **Execute commit** using the drafted message
4. **Verify success** with `git status`

## Important Constraints

- **Only commit when explicitly instructed**: Wait for the exact signal "COMMIT"
- **Never run `git add`**: File staging is the user's responsibility
- **Handle pre-commit hooks**: If hooks modify files, amend the commit to include them
- **Fail gracefully**: If commit fails, explain the issue and suggest solutions

## Error Handling

- If pre-commit hooks change files, retry commit once to include automated changes
- If hooks prevent commit, explain the blocking issue
- Never use `--no-verify` to bypass hooks
- If no changes are staged, inform user instead of creating empty commit

## Success Criteria

- [ ] Commit message matches repository style
- [ ] Message accurately reflects staged changes
- [ ] All quality checks pass
- [ ] No AI attribution included
- [ ] Commit succeeds without bypassing hooks

Remember: The commit message should tell future developers (including the user) why this change was made and what problem it solves, not just what files were modified.