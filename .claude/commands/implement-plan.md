---
description: Execute staged implementation plan following development guidelines
---

# Implement Plan

You are tasked with executing an implementation plan following the development guidelines. Follow these principles:

## Core Philosophy
- **Simple, elegant, minimal solutions** - Every line must earn its place, zero unnecessary complexity
- **Bug-free over feature-rich** - Correctness trumps everything, zero tolerance for subtle errors
- **Incremental progress over big bangs** - Complete one stage before moving to next
- **Learning from existing code** - Study patterns before implementing
- **Single responsibility per stage** - Each stage should be independently deliverable
- **Maximum 3 attempts per issue** - Document failures and try different approach

## Setup
1. If an implementation file path is provided as an argument: {{ARGS}}, use that file
2. If no argument is provided, infer the implementation file from the conversation context:
   - Look for recently mentioned `.plan/` files
   - Check for `.plan/` directory in current working directory and use the most recent file
   - If multiple files exist, ask the user to specify which one to use

## Implementation Flow

### 1. Status Check Phase
- Read and parse the implementation plan
- Check current status of all stages:
  - **No stages started**: Do nothing and exit
  - **Completed stages**: Verify changes related to these stages exist
  - **In Progress stages**: Handle continuation logic (see below)
- Only proceed if there are actionable stages

### 2. In Progress Stage Handling
For any stage marked "In Progress":
- Check if changes related to this stage are uncommitted:
  - **Uncommitted changes**: Stash changes and restart stage from beginning
  - **Committed changes**: Study existing commits and continue from where left off
- Use git log and diff to understand current implementation state

### 3. Understanding Phase
- Read and understand the complete implementation plan
- Study existing codebase patterns and conventions
- Identify 3 similar implementations for reference
- Verify all dependencies and requirements

### 4. Stage-by-Stage Execution
- Execute each stage in exact order specified
- Update stage status in plan file as you progress
- Complete one stage fully before moving to next
- Follow the 4-step process for each stage:
  1. **Understand** - Study existing patterns for simplest, most elegant approach
  2. **Test** - Write test first (if tests exist in codebase) - design for zero bugs
  3. **Implement** - Minimal, elegant code that achieves stage goal - nothing extra
  4. **Validate** - Run quality checks and verify zero bugs, maximum simplicity

### 5. After Each Stage Completion
1. **Update plan status** to "Complete"
2. **Analyze actual changes made** (not just task completed)
3. **Present changes for verification**:
   - Show what was implemented
   - List files that were modified/created
   - **IMPORTANT: PAUSE and wait for `PROCEED` signal**
   - User may manually adjust code during this verification phase

### 6. When Stuck (Maximum 3 Attempts)
1. **Document what failed**: What you tried, errors, why it failed
2. **Research alternatives**: Find 2-3 different approaches
3. **Question fundamentals**: Is this the right abstraction level?
4. **Try different angle**: Different library/pattern/approach

## Critical Rules
- **IMPORTANT: Never run git write operations** - Only readonly git commands (status, diff, log) allowed - User handles all git modifications
- **IMPORTANT: Only proceed to next stage when `PROCEED` signal given**
- **Follow existing code conventions** found in codebase
- **Prioritize editing existing files** over creating new ones
- **Remove plan file when all stages complete**
- **Run available quality checks** (linters, formatters, build scripts)

## Process Flow
1. Load implementation plan and check all stage statuses
2. **Status-based actions**:
   - If no stages started: Exit with no action
   - If stages completed: Verify related changes exist
   - If stage in progress: Handle continuation (stash uncommitted or study commits)
3. Begin with first actionable stage
4. For each stage:
   - Update status to "In Progress"
   - Execute using 4-step process
   - Update status to "Complete"
   - **Present changes for user verification**
   - **IMPORTANT: Wait for `PROCEED` signal (user may manually adjust code during this time)**
   - When `PROCEED` is received, check for any manual changes and adapt plan if necessary
   - Proceed to next stage
5. **Self-review phase**: After all stages complete, ruthlessly review all changes and remove any parts that were not absolutely necessary for the task. The final result must be minimal, elegant, and completely bug-free.
6. Remove plan file when all stages complete

Remember: You implement the code changes. The user controls all git operations entirely.