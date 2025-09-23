---
allowed-tools: Bash(mkdir:*), Bash(cursor:*), Write
description: Create a staged implementation plan aligned with development guidelines
---

# Create Implementation Plan

You are tasked with creating an implementation plan aligned with the development guidelines in CLAUDE.md. Follow these guidelines:

## Planning Approach
- **Simple, elegant, minimal solutions** - Each stage must produce zero-bug, minimal code
- **Break work into 3-5 stages maximum** - Each stage should be independently deliverable
- **If more than 5 stages needed** - Split into multiple planning documents (append part-{num} to filename)
- **Incremental progress over big bangs** - Small changes that compile and pass tests
- **Learn from existing code** - Study patterns before implementing
- **Pragmatic over dogmatic** - Adapt to project reality
- **Bug prevention over complexity** - Design stages to eliminate entire error classes

## Setup
1. Create a `.plan/` directory in the current working directory if it doesn't exist
2. **Always generate a new markdown implementation plan file** in the `.plan/` directory with a descriptive filename
3. If a related plan file already exists, you may reference it for context, but **create a fresh plan based on the current user instruction**
4. If the work requires more than 5 stages, create multiple files (e.g., `feature-implementation-part-1.md`, `feature-implementation-part-2.md`)

## Process
1. **Understand** - Study existing codebase patterns and conventions
2. **Analyze** the user's requirements: {{ARGS}}
3. Create the implementation plan in `.plan/` directory
4. After creating the plan file, automatically open it in the user's active IDE using: `cursor -r ./.plan/{file_name}`
5. Present the plan and wait for feedback

## Required Plan Structure

```markdown
# Implementation Plan

## Overview
[Brief description of what will be built]

## Codebase Analysis
- Existing patterns found
- Libraries/frameworks in use
- Testing approach (if any)
- Code conventions observed

## Stage 1: [Name]
**Goal**: [Specific deliverable]
**Success Criteria**: [Testable outcomes]
**Tests**: [Specific test cases - skip if no existing test framework]
**Status**: [Not Started|In Progress|Complete]

### Sub-steps:
- [ ] [Step 1] - [atomic, git commit-able change]
- [ ] [Step 2] - [atomic, git commit-able change]
- [ ] [Step 3] - [atomic, git commit-able change]

## Stage 2: [Name]
...

[Repeat for 3-5 stages total]

## Quality Gates
- [ ] **Zero bugs** - Implementation is completely correct and robust
- [ ] **Minimal code** - Only essential code remains, maximum simplicity achieved
- [ ] **Elegant design** - Solution is obvious and reads naturally
- [ ] Code compiles
- [ ] Existing tests pass (if any)
- [ ] Linting/formatting passes
- [ ] Manual testing complete

## Self-Review Phase
**After all stages complete**: Ruthlessly review all changes and remove any parts that were not absolutely necessary for the task. The final solution should be minimal, elegant, and completely bug-free.
```

## Implementation Guidelines
- **Minimal viable implementation** - Build only what's absolutely necessary
- **Elegant, self-documenting code** - Solutions that read like prose
- **Zero-bug tolerance** - Correctness over complexity, every time
- **Single responsibility** per stage
- **No abstractions unless essential** - Choose boring, obvious solutions
- **Test-driven when tests exist** - Never disable existing tests
- **Follow project conventions** - Use existing libraries and patterns
- **Composition over inheritance** - Prefer dependency injection
- **Fail fast** with descriptive error messages
- **Ruthless simplification** - Remove any code that doesn't earn its place

## Critical Rules
- **Never run git write operations** - Only readonly git commands (status, diff, log) allowed - User handles all git modifications
- **Only proceed to next stage when `PROCEED` signal given**
- **Maximum 3 attempts per issue** - Document failures and try different approach
- **Study 3 similar implementations** before starting
- **Remove this file when all stages complete**