---
name: refine-feature
description: >-
  Use when the user wants to refine, specify, or discuss a new feature idea
  before implementation. Triggers on phrases like "Lass uns Feature X
  verfeinern", "Ich habe eine Idee für Feature Y", "Können wir Feature Z
  besprechen", "Refine Feature X". Works for any product type (frontend,
  backend, mobile, etc.). Guides a structured conversation to clarify
  requirements, terminology, and user groups, then documents everything as
  Markdown files.
---

# Refine Feature

## Overview

Structured feature refinement conversation that produces:

1. Updated `docs/explanation/product.md` – product-level knowledge base (ubiquitous language, user groups)
2. `docs/features/<feature-slug>.md` – feature specification (user story, acceptance criteria, motivation)

**Preconditions:** The `docs/` directory is created automatically if it doesn't exist.

**Language:** Conduct the entire conversation in the same language the user uses.

## Context Status

After every phase, output a context status line:

```
📊 Kontext: [████████░░] ~80% — Phase N abgeschlossen
```

Use a rough estimate:
- Phase 1 done → ~20%
- Phase 2 done → ~40%
- Phase 3 done → ~55%
- Phase 4 done → ~70%
- Phase 5 done → ~100%

---

## Process Flow

```
Phase 1 (Feature verstehen)
  → Phase 2 (Begriffe & Ubiquitous Language)
    → Phase 3 (Nutzergruppen)
      → Phase 4 (Akzeptanzkriterien)
        → Phase 5 (Dokumentation schreiben & committen)
```

---

## Phase 1 – Feature Understanding

If the user hasn't described the feature yet, ask them to do so first.

Then ask clarifying questions **one at a time** using `AskUserQuestion`. Focus only on business/domain understanding – no technical questions:

- What problem does this feature solve?
- Who benefits from it, and how?
- What is the expected behavior from the user's perspective?
- Are there any constraints, boundaries, or edge cases to consider?

Continue asking until the feature is sufficiently clear. When done, summarize the feature in 3–5 sentences and ask:

> "Ist das eine korrekte Zusammenfassung? Dann gehen wir weiter zur Klärung der Fachbegriffe."

Wait for confirmation before proceeding.

**Output after confirmation:**

```markdown
## Feature-Zusammenfassung
[3–5 sentences]

## Offene Punkte
[any unresolved questions – leave empty if none]
```

Then output context status.

---

## Phase 2 – Terminology & Ubiquitous Language

Identify all domain-specific terms and concepts that appeared during Phase 1.

**Before starting:** Check if `docs/explanation/product.md` exists and read it. Note which terms are already defined there – do not redefine them unless the user explicitly wants to update a definition.

For each new term:
1. Propose a clear, concise definition based on the conversation so far
2. Ask the user via `AskUserQuestion` whether the definition is correct – adjust as needed

Present all identified terms and their definitions in a table. Ask:

> "Sind diese Begriffe korrekt definiert? Fehlt noch ein wichtiger Begriff?"

Wait for confirmation or corrections before proceeding.

**Output after confirmation:**

```markdown
## Neue / Aktualisierte Begriffe

| Begriff | Definition |
|---------|-----------|
| [Term]  | [Definition] |
```

If no new terms were identified, state this explicitly and proceed.

Then output context status.

---

## Phase 3 – User Groups

Identify which user groups interact with this feature.

**Before starting:** Read `docs/explanation/product.md` to know which user groups already exist.

1. Determine which existing groups are relevant for this feature and how they interact with it
2. Ask if any new user groups need to be introduced for this feature
3. For each new group: clarify who they are, what their role is, and what their goals are

Present findings and ask:

> "Sind alle betroffenen Nutzergruppen vollständig erfasst?"

Wait for confirmation before proceeding.

**Output after confirmation:**

```markdown
## Betroffene Nutzergruppen

- **[Group]**: [their role in this feature]

## Neue Nutzergruppen

- **[Group]**: [full description, role, goals]
```

If no new groups are needed, state this explicitly.

Then output context status.

---

## Phase 4 – Acceptance Criteria

Define clear, testable acceptance criteria for the feature.

Start from the feature summary and acceptance signals that emerged during the conversation. For each criterion, use one of these formats:

- **Scenario format:** "Gegeben [Vorbedingung], wenn [Aktion], dann [Erwartetes Ergebnis]"
- **Bullet format:** For simpler UI/UX or non-interactive criteria

Draft a list and present it. Ask:

> "Sind diese Akzeptanzkriterien vollständig und korrekt? Sollen wir etwas ergänzen oder anpassen?"

Iterate until the user confirms. Aim for 3–8 criteria that cover the happy path and the most important edge cases.

**Output after confirmation:**

```markdown
## Akzeptanzkriterien

- Gegeben [X], wenn [Y], dann [Z]
- ...
```

Then output context status.

---

## Phase 5 – Documentation

Write the documentation. No further questions – write based on everything discussed.

### 5a – Update `docs/explanation/product.md`

Read the file if it exists. Then update or create it with the following structure. Merge new content into existing content – **never remove existing entries** unless explicitly instructed.

```markdown
# [Product Name]

## Beschreibung

[Short product description]

## Nutzergruppen

### [Group Name]

[Description of the user group, their goals, and context]

## Ubiquitäre Sprache

| Begriff | Definition |
|---------|-----------|
| [Term]  | [Definition] |
```

**If the file doesn't exist:** Ask the user for the product name and a short description before creating it.

**Rules:**
- Preserve existing writing style and tone
- Only add new terms and user groups from this refinement session
- Keep alphabetical order within the glossary table

### 5b – Create `docs/features/<feature-slug>.md`

Derive the slug from the feature name: lowercase, words separated by hyphens, no special characters.
Example: "Benutzer Benachrichtigung" → `benutzer-benachrichtigung`

Create the file with this structure:

```markdown
# Feature: [Feature Name]

## Motivation

[Why is this feature needed? What problem does it solve? What triggered this request?]

## Nutzergruppen

[Which user groups are affected and how they interact with this feature]

## Beschreibung

[Detailed description of the feature from a user perspective. No implementation details.]

## Akzeptanzkriterien

- Gegeben [X], wenn [Y], dann [Z]
- ...

## Offene Fragen

[Unresolved questions or follow-up topics from the refinement session. Leave empty if none.]
```

### 5c – Commit

After both files are written, commit following the [commit skill](./../commit/SKILL.md) with type `ai(docs)`:

```
ai(docs): refine feature <feature-name>
```

Then output a final summary:

```
✅ Refinement abgeschlossen!

📄 Produktdokumentation aktualisiert: docs/explanation/product.md
📄 Feature-Dokumentation erstellt:    docs/features/<feature-slug>.md
```
