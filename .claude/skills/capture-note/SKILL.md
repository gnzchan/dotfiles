---
name: capture-note
description: |
  Capture a quick note or todo into the Obsidian vault, filed under the right
  project. Use when the user wants to jot something down, park an idea, or add
  a follow-up: "capture this", "note to self", "add a todo to <project>", "jot
  this down", "remember to", "quick note", "add to my notes". Files it as a
  checkbox under the project's note, nested beneath the feature it belongs to.
  Not for long-form docs; this is fast capture as checkboxes.
---

# Capture note

Fast capture into the Obsidian vault. One note file per project, todos as
checkboxes, each nested under the feature it belongs to. Keep it terse.

## Vault layout

Vault root: `/Users/christiangonzales/Documents/MB-Pro`

Project notes live at `Projects/<Company>/<Project>.md`. Companies are the
folders under `Projects/`:

- `DealFuel`
- `GK Marketing Services`
- `Personal`

The **filename is the project name**, in the case it should display (e.g.
`GK Call Analytics.md`). Obsidian shows the filename as the title, so the note
body has **no `# H1`** and no frontmatter.

## How a project note is structured

```
## <Feature>

### <group, optional>

- [ ] a todo
- [ ] another todo
```

- A feature (a domain or area of the project) is an `##` heading.
- An optional `###` groups todos within a feature ("Next", "Cleanup", etc.).
- Every captured item is a checkbox `- [ ]`. Skip prose unless one short line
  genuinely helps orient the reader.

## Process

1. **Resolve the project.** If the user names one, or you are clearly working
   in a repo that maps to a project, use it. Otherwise ask with the
   AskUserQuestion tool which company it belongs to: DealFuel, GK Marketing
   Services, or Personal (the "Other" option covers a new company, in which
   case create the folder). Then get the project name, which is the filename.
   Ask only if you cannot infer it.

2. **Find or create the note** at `Projects/<Company>/<Project>.md`.
   - New file: start with `## <Feature>` and the checkbox(es) under it. Ask for
     the feature only when you cannot infer it from the note being captured.
   - Existing file: read it first, then place the checkbox under the matching
     `##` feature (and `###` group if that feature uses them). Add a new `##`
     feature only for a genuinely new area of the project.

3. **Write the item as a checkbox.** One line, specific, terse. Do not add an
   item that already exists in the note.

4. **Humanize.** Run the [[humanizer]] pass over anything past the bare
   checkbox text (a feature name, a one-line description). No fluff, no filler,
   no em dashes. The list should read like a person wrote it, not a model.

## Rules

- Filename is the project name and nothing else. No dates, no prefixes.
- No `# H1` in the body (the filename is the title) and no frontmatter.
- Checkboxes for items; features and groups are headings.
- Ask the minimum. Skip any question you can answer from context.
- Append, never overwrite. Preserve everything already in the note.

## Example

Capturing "track OpenAI cost per clinic" for the GK Call Analytics project,
under its Content Generation feature, appends:

```
### Next (deferred from V1)

- [ ] Track OpenAI cost per clinic
```

to `Projects/GK Marketing Services/GK Call Analytics.md`, under the existing
`## Content Generation` heading.
