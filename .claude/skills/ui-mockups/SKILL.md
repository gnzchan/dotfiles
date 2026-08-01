---
name: ui-mockups
version: 1.0.0
description: |
  Brainstorm UI by building live, interactive design options at a throwaway
  /mockup route inside the actual app, using the project's real tokens and
  components so options are honest previews, not pictures. Use when the user
  wants to explore design directions, compare interaction patterns, or
  cherry-pick between variants before committing to an implementation —
  "give me options", "mock this up", "how should this look/feel".
allowed-tools:
  - Read
  - Glob
  - Grep
  - Write
  - Edit
  - Bash
---

# UI mockups

Build N live design options at a dev-only mockup route inside the real app.
The point of in-app (vs. a static artifact or screenshots): options render
with the real theme tokens, real components, and real chrome, and
interactions (hover, click, open, animate) actually work — so what the user
picks is what they get.

## Step 1: pin the question and the axes

Before writing code, state the single design question ("how does metric
drill-down look and feel?") and the axes being compared (affordance style,
container type, layout). Options must differ on real axes, not decoration.
Four options is the default; each gets a short name and a one-line thesis.

If the user's question is about *data display* (charts, palettes, stat
tiles), check the dataviz skill first and validate any new colors — never
eyeball palettes.

## Step 2: inherit, never fork

- Read the design system first: `globals.css` (or the project's token file),
  existing shared components, chart config. Options use tokens and existing
  components only — no literal colors, no parallel styles.
- Check shadcn (or the project's component library) before hand-rolling
  anything; install missing primitives (`npx shadcn@latest add <x>`) rather
  than approximating them, since the winning option ships on the real
  component anyway. If the CLI prompts to overwrite existing files, decline.

## Step 3: build the route

- Next.js App Router: `src/app/(app)/mockup/page.tsx` — inside the
  authenticated layout group if one exists, so the real header/chrome frames
  the options (the page inherits the group's auth; skip any page-specific
  data fetching or redirect logic). Other stacks: the equivalent dev route.
- Gate it from the start — `if (process.env.NODE_ENV === 'production')
  notFound()` — cheap insurance against the route outliving the exercise.
- Structure: one shared mock-data module + one component per option under
  `_components/`. Client components where interaction is the subject.
- **Same subject, same mock data across all options** — the user compares
  treatments, not content. Use the app's real domain shapes (real column
  names, realistic values), never lorem.
- Each option on the page: `Option N — Name`, thesis line, the live demo,
  and 2–4 "what changes" notes (scope, risk, feel).
- Keep the page chrome quiet (eyebrow labels, hairlines); the demos are the
  show. Respect `prefers-reduced-motion` in any animated affordance.
- **When the thing being designed is an insert into an existing component or
  flow (a nudge in a modal, a field in a form, a badge in a row), render the
  REAL host component with the variant slotted in, not a hand-drawn stand-in.**
  A mimicked container reads as unfamiliar and the user can't tell where the
  new piece actually lands. Add a minimal optional slot prop to the host
  (e.g. `nudge?: ReactNode`, defaults to nothing so production is unchanged),
  drive it from the mockup, and mount the host live (feed it mock props / let
  it hit real dev data). That slot is usually the real integration seam, so it
  carries into implementation rather than being thrown away. Give the user a
  variant toggle so each option renders inside the same real host.

## Step 4: present and decide

- Tell the user the route URL and how the options map to the axes, and
  invite cherry-picking across options ("Option 2's overlay with Option 4's
  drawer") — the axes make hybrids cheap.
- Iterate in place: edit the option components, hot reload does the rest.

## Step 5: record, then delete

The route is throwaway. Once the user decides:

- Record the decision where it's durable — the project's planning
  convention (design doc, spec, `.plan/`), not the mockup code.
- Delete the mockup route in the same change that implements the real
  feature. Primitives installed for the mockups: keep what the winning
  option uses, remove the rest in that same change.

## Rules

- **Live beats static.** If an option's core idea is an interaction, the
  interaction must work in the demo.
- **One fetch of truth.** Mock data lives in one module; options import it.
- **Name options for reference.** Numbers + names ("Option 3 — Unfold") so
  feedback like "3 but with 1's affordance" is unambiguous.
- **Don't skip the weird one.** At least one option should take a real
  risk; identical-but-recolored options waste the exercise.

## Related skills

- `dataviz` for chart forms and palette validation when options involve data
  display.
- `humanizer` if option notes start reading like AI prose.
