---
allowed-tools: Bash(mkdir:*), Bash(cursor:*), Write
description: Create a Test-Driven Development implementation plan aligned with development guidelines
---

# Create TDD Implementation Plan

You are tasked with creating a Test-Driven Development (TDD) implementation plan aligned with the development guidelines in CLAUDE.md. Follow these guidelines:

## TDD Planning Approach

- **Test-first philosophy** - Write failing tests before implementation code
- **Red-Green-Refactor cycle** - Each stage follows: failing test → minimal implementation → refactor
- **Simple, elegant, minimal solutions** - Each stage must produce zero-bug, minimal code
- **Break work into 3-5 stages maximum** - Each stage should be independently deliverable and testable
- **If more than 5 stages needed** - Split into multiple planning documents (append part-{num} to filename)
- **Incremental progress over big bangs** - Small changes that compile and pass tests
- **Learn from existing code** - Study patterns and testing approaches before implementing
- **Pragmatic over dogmatic** - Adapt to project reality while maintaining TDD principles
- **Bug prevention over complexity** - Design stages to eliminate entire error classes through tests

## Setup

1. Create a `.plan/` directory in the current working directory if it doesn't exist
2. **Always generate a new markdown implementation plan file** in the `.plan/` directory with a descriptive filename ending in `-tdd.md`
3. If a related plan file already exists, you may reference it for context, but **create a fresh plan based on the current user instruction**
4. If the work requires more than 5 stages, create multiple files (e.g., `feature-implementation-tdd-part-1.md`, `feature-implementation-tdd-part-2.md`)

## Process

1. **Understand** - Study existing codebase patterns, conventions, and testing frameworks
2. **Analyze** the user's requirements: {{ARGS}}
3. **Identify testing strategy** - Determine unit, integration, and end-to-end test requirements
4. Create the TDD implementation plan in `.plan/` directory
5. After creating the plan file, automatically open it in the user's active IDE using: `cursor -r ./.plan/{file_name}`
6. Present the plan and wait for feedback

## Required TDD Plan Structure

```markdown
# TDD Implementation Plan

## Overview

[Brief description of what will be built using Test-Driven Development]

## Codebase Analysis

- Existing patterns found
- Libraries/frameworks in use
- **Testing framework identified**: [Jest/Vitest/Mocha/PyTest/etc.]
- **Test file locations**: [__tests__/, test/, spec/, etc.]
- **Test naming conventions**: [*.test.js, *.spec.ts, etc.]
- **Mocking libraries available**: [jest.mock, sinon, etc.]
- Code conventions observed

## TDD Strategy

- **Test types needed**: [Unit/Integration/End-to-End]
- **Test boundaries**: [What to mock vs. test directly]
- **Test data approach**: [Fixtures/Factories/Inline data]
- **Assertion style**: [expect/should/assert]

## Stage 1: [Name] - TDD Cycle

**Goal**: [Specific deliverable]
**Success Criteria**: [Testable outcomes - tests pass]
**TDD Cycle**: Red → Green → Refactor
**Status**: [Not Started|In Progress|Complete]

### Red Phase - Write Failing Tests:

- [ ] Write test for [specific behavior 1] - should fail initially
- [ ] Write test for [specific behavior 2] - should fail initially
- [ ] Write test for [edge case/error condition] - should fail initially
- [ ] Verify all tests fail with expected error messages

### Green Phase - Minimal Implementation:

- [ ] [Step 1] - implement minimal code to pass first test
- [ ] [Step 2] - implement minimal code to pass second test
- [ ] [Step 3] - handle edge case to pass third test
- [ ] Verify all tests now pass

### Refactor Phase - Improve Design:

- [ ] [Step 1] - refactor implementation while keeping tests green
- [ ] [Step 2] - optimize or simplify code structure
- [ ] [Step 3] - ensure code follows project conventions
- [ ] Final verification: all tests still pass

## Stage 2: [Name] - TDD Cycle

...

[Repeat for 3-5 stages total]

## Quality Gates

- [ ] **All tests pass** - Complete test suite runs successfully
- [ ] **Test coverage adequate** - Critical paths and edge cases covered
- [ ] **Zero bugs** - Implementation is completely correct and robust
- [ ] **Minimal code** - Only essential code remains, maximum simplicity achieved
- [ ] **Elegant design** - Solution is obvious and reads naturally
- [ ] **Tests are maintainable** - Clear, focused, and easy to understand
- [ ] Code compiles
- [ ] Existing tests still pass
- [ ] Linting/formatting passes
- [ ] Manual testing complete (if applicable)

## Self-Review Phase

**After all stages complete**:

1. **Test Review** - Ensure tests are comprehensive, clear, and maintainable
2. **Implementation Review** - Ruthlessly review all changes and remove any parts that were not absolutely necessary
3. **TDD Adherence** - Verify that tests were written before implementation code
4. **Final Validation** - Run complete test suite and ensure 100% pass rate

The final solution should be minimal, elegant, completely bug-free, and thoroughly tested.
```

## TDD Implementation Guidelines

- **Write tests first, always** - No implementation code before failing tests exist
- **Minimal viable implementation** - Build only what's needed to pass tests
- **One test, one behavior** - Each test should verify a single, specific behavior
- **Descriptive test names** - Tests should read like specifications
- **Fast feedback loops** - Tests should run quickly for rapid iteration
- **Test edge cases and errors** - Don't just test the happy path
- **Mock external dependencies** - Isolate units under test
- **Refactor with confidence** - Tests provide safety net for improvements
- **Test-driven design** - Let tests influence better API design
- **Fail fast** with descriptive error messages in both tests and implementation
- **Single responsibility** per stage and per test
- **No abstractions unless essential** - Choose boring, obvious solutions
- **Follow project test conventions** - Use existing test utilities and patterns
- **Composition over inheritance** - Prefer dependency injection for testability

## TDD-Specific Rules

- **Red phase mandatory** - Every test must fail initially with the expected reason
- **Green phase minimal** - Write only enough code to make tests pass
- **Refactor phase disciplined** - Improve design without changing behavior
- **Never skip tests** - If it's worth implementing, it's worth testing
- **Test behavior, not implementation** - Focus on what the code does, not how
- **One failing test at a time** - Keep feedback loop tight and focused
- **Commit after each green phase** - Maintain working state at all times

## Critical Rules

- **Never run git write operations** - Only readonly git commands (status, diff, log) allowed - User handles all git modifications
- **Only proceed to next stage when `PROCEED` signal given**
- **Maximum 3 attempts per issue** - Document failures and try different approach
- **Study 3 similar test implementations** before starting
- **Remove this file when all stages complete**
- **Tests must exist before any implementation code** - No exceptions to TDD flow
