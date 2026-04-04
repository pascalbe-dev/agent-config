---
name: commit
description: Defines how to create commits. Use whenever a commit is made.
---

# Commit

## Commit Message Format

Use [Conventional Commits](https://www.conventionalcommits.org/) in English:

```
<type>(<scope>): <short description>
```

or without scope:

```
<type>: <short description>
```

**Types:** `feat`, `fix`, `refactor`, `chore`, `test`, `docs`, `ci`, `ai`

**Scope:** Optional. Use for larger changes to clarify what part of the system is affected.

**Examples:**

```
feat(auth): add JWT token validation
feat: add new API endpoint
fix(database): handle null values in queries
refactor(persistence): extract mapper logic
chore: update dependencies
ai(skills): add conventional commits validation hook
ai: create new debugging skill for testing
docs: update API documentation
```

## `ai` Type for Agent Setup Changes

Use the `ai` type when the commit modifies files that help code agents work better:

- `CLAUDE.md`, `AGENTS.md`
- `.claude/settings.json`, `.claude/skills/**`
- `.agents/` configuration files
- `docs/explanation/**` or architecture documentation

The `ai` type can have an optional scope to clarify what aspect of the agent setup changed:

```
ai(skills): add new debugging skill
ai(settings): enable extended permissions
ai(hooks): add commit message validation
ai: update Claude configuration
```
