---
allowed-tools: Bash(git:*), Bash(find:*), Bash(grep:*), Bash(ls:*), Read, Write, Edit, MultiEdit, Glob, Grep, WebFetch, WebSearch, Task
description: Implement beautiful, elegant frontend features with mock data and consistent UI/UX design
---

# Implement Frontend

You are tasked with implementing frontend features that are beautiful, elegant, and consistent with the existing design while following KISS and SOLID principles.

## Input Handling

The command accepts input in multiple ways:
1. **File path**: {{ARGS}} - Read implementation details from a file
2. **Image path**: {{ARGS}} - Read and analyze a design mockup or wireframe image
3. **Text description**: {{ARGS}} - Use the provided text as feature requirements
4. **Combination**: {{ARGS}} - Mix of file paths, image paths, and text descriptions

The command will intelligently parse the input and handle each type appropriately.

## Core Philosophy

- **Simple, elegant, minimal solutions** - Beautiful but not complex
- **Consistent with existing design** - Study and match current UI patterns
- **Frontend-only implementation** - Use mock data with easy backend integration points
- **Modern and pretty** - Clean, beautiful, but maintainable design
- **KISS and SOLID principles** - Keep it simple, follow solid architecture
- **Easy backend integration** - Clear TODO markers for backend connections

## Implementation Flow

### 1. Analysis Phase

**Input Processing:**
- If file path provided: Read and parse the implementation file
- If image path provided: Analyze the design mockup or wireframe to understand UI requirements
- If text provided: Use as feature requirements directly
- If combination provided: Process each input type and synthesize requirements
- Validate input and ensure requirements are clear

**Research Phase:**
- **UI/UX Research**: Search for best practices and modern patterns for the feature type
  - Look up current design trends for similar features
  - Research accessibility considerations
  - Find elegant, minimal design examples
- **Codebase Analysis**: Study existing patterns and conventions
  - Identify current UI framework/library (React, Vue, Angular, etc.)
  - Find existing components and design patterns
  - Analyze current styling approach (CSS modules, styled-components, Tailwind, etc.)
  - Study color schemes, typography, spacing patterns
  - Identify existing loading and error state patterns
  - **Infer component structure** from project setup and existing patterns

### 2. Design Planning

**Consistency Analysis:**
- Map out existing design system elements
- Identify reusable components and utilities
- Determine styling approach to match current patterns
- Plan component structure following existing conventions

**Feature Architecture:**
- Design component hierarchy following SOLID principles
- Plan state management approach (local state, context, store)
- Design mock data structure that mirrors expected backend API
- Plan loading and error state implementations

### 3. Implementation Phase

**Component Creation:**
- Follow existing component patterns and naming conventions
- Implement clean, readable component structure
- Use existing styling patterns and design tokens
- Ensure responsive design following current breakpoint patterns

**Mock Data Integration:**
- Create realistic mock data that represents actual backend responses
- Add artificial delays (500-1500ms) for loading states
- Implement error simulation for robust error handling
- Add clear TODO comments for backend integration points:
  ```javascript
  // TODO: Should fetch user profile data from API
  // TODO: Should update user data here
  // TODO: Should delete item via API call
  ```

**State Management:**
- Implement loading states with elegant loading indicators
- Add comprehensive error handling with user-friendly messages
- Follow existing error handling patterns in the codebase
- Ensure state updates are clean and predictable

### 4. Git Integration & Commits

**Atomic Commit Strategy:**
- Create small, focused commits for each logical change
- Each commit should be runnable and not break functionality
- Use `git add` and `git commit` for each atomic change
- Follow repository's existing commit message format

**Commit Flow:**
1. **Initial setup**: Add basic component structure
2. **Styling**: Add styles matching existing design
3. **Mock data**: Implement mock data and state management
4. **Loading states**: Add loading indicators and transitions
5. **Error handling**: Implement error states and recovery
6. **Responsive design**: Ensure mobile/tablet compatibility
7. **Accessibility**: Add ARIA labels and keyboard navigation
8. **Polish**: Final refinements and code cleanup

### 5. Quality Assurance

**Code Quality:**
- Run available linters and formatters
- Ensure TypeScript compliance (if applicable)
- Verify accessibility standards
- Test responsive behavior across breakpoints

**Integration Testing:**
- Verify component integrates cleanly with existing layout
- Test loading and error states
- Ensure consistent styling with existing components
- Validate that TODOs are clear for backend integration

## Mock Data Guidelines

**Structure:**
```javascript
// Example mock service structure
const mockApiCall = async () => {
  // TODO: Should replace with actual API call to /api/users

  // Simulate network delay
  await new Promise(resolve => setTimeout(resolve, 800));

  // Simulate occasional errors (10% chance)
  if (Math.random() < 0.1) {
    throw new Error('Network error - please try again');
  }

  return mockData;
};
```

**Data Realism:**
- Use realistic data that matches expected backend structure
- Include edge cases (empty states, long text, missing fields)
- Provide variety in data for thorough testing
- Include proper data types and validation

## Styling Guidelines

**Consistency Requirements:**
- Use existing color palette and design tokens
- Follow current spacing and typography scale
- Match existing component sizing and proportions
- Use consistent animation and transition patterns

**Modern Design Principles:**
- Clean, minimal layouts with appropriate whitespace
- Subtle shadows and borders following existing patterns
- Consistent iconography and visual hierarchy
- Smooth interactions and micro-animations (if existing)

## Component Structure

**Infer from Project:**
- Study existing component organization patterns
- Follow current file naming conventions
- Use existing directory structure
- Match current import/export patterns
- Adapt to project's chosen architecture (feature-based, atomic design, etc.)

## Error Handling Standards

**Loading States:**
- Skeleton loaders or spinner following existing patterns
- Progressive loading for better perceived performance
- Graceful degradation when data is unavailable

**Error States:**
- User-friendly error messages
- Retry mechanisms where appropriate
- Fallback UI when features are unavailable
- Consistent error styling with existing patterns

## Git Commit Examples

**Atomic Commit Messages:**
```
feat: add user profile component structure
style: implement profile styling with existing design tokens
feat: add mock user data with loading simulation
feat: implement loading states for profile component
feat: add error handling and retry mechanism
style: ensure responsive design for profile component
feat: add accessibility attributes to profile component
refactor: extract reusable profile card component
```

## Critical Rules

- **Study before implementing**: Always analyze existing patterns first
- **Consistency over novelty**: Match existing design over new patterns
- **Mock data must be realistic**: Structure should match expected backend
- **Clear integration points**: TODOs must be specific and actionable
- **Responsive by default**: All components must work across breakpoints
- **Accessibility compliance**: Follow existing a11y patterns
- **Performance conscious**: Avoid unnecessary re-renders and heavy operations
- **Atomic commits**: Each commit should be small, focused, and runnable
- **Infer structure**: Don't assume - study the project's actual patterns

## Success Criteria

- [ ] **Beautiful and elegant** - Visually appealing and modern design
- [ ] **Consistent design** - Matches existing UI patterns perfectly
- [ ] **Realistic mock data** - Easy to replace with backend integration
- [ ] **Proper loading states** - Elegant loading indicators with delays
- [ ] **Robust error handling** - Comprehensive error states and recovery
- [ ] **Clean architecture** - Follows SOLID and KISS principles
- [ ] **Clear TODOs** - Specific integration points for backend
- [ ] **Responsive design** - Works across all existing breakpoints
- [ ] **Accessible** - Follows existing accessibility patterns
- [ ] **Atomic commits** - Each commit is focused and runnable
- [ ] **Inferred structure** - Follows actual project patterns

Remember: The goal is to create frontend features that look and feel like they belong in the existing application while being easy to connect to real backend data later. Always study the existing codebase to infer proper structure and patterns.