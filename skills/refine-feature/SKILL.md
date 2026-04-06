---
name: refine-feature
description: >-
  Verwenden wenn der Nutzer ein Feature verfeinern, spezifizieren oder besprechen möchte.
  Trigger: "Lass uns Feature X verfeinern", "Ich habe eine Idee für Feature Y",
  "Können wir Feature Z besprechen". Funktioniert für alle Produkttypen (Frontend,
  Backend, Mobile, etc.).
---

# Feature Refinement

Iterative Spezifikation neuer Features oder Erweiterungen bestehender Features.

**Sprache:** In der Sprache des Nutzers kommunizieren.

---

## Vorbereitung

Stillschweigend lesen:
- `docs/explanation/product.md` – Falls vorhanden, bestehende Nutzergruppen und Begriffe notieren
- `docs/features/` – Bestehende Features

---

## Phase 1 – Feature verstehen

Fragen (eine nach der anderen):
1. Problem/Nutzen?
2. Betroffene Nutzergruppen?
3. Constraints oder Randfälle?

Feature kurz zusammenfassen, Bestätigung abwarten.

---

## Phase 2 – Begriffe & Ubiquitous Language

Neue Fachbegriffe aus Phase 1 identifizieren. Für jeden Begriff erfragen:
- **Deutsch:** [Begriff]
- **English (Code):** [Von Nutzer erfragen, nicht vorschlagen]
- **Definition:** [Kurz erklären]

Tabelle bei Bestätigung:
```markdown
| Deutsch | English (Code) | Definition |
|---------|----------------|-----------|
```

---

## Phase 3 – Nutzergruppen

Klären: Welche bestehenden Nutzergruppen sind betroffen? Sind neue nötig?

---

## Phase 4 – Datenfelder (Optional)

Falls nötig: Klären, welche Daten das Feature erfasst/nutzt.

---

## Phase 5 – User Stories & Akzeptanzkriterien

Pro Story:
- Klären: MVP-Scope? (Was gehört rein, was nicht?)
- Akzeptanzkriterien definieren (nur Nutzer-Mehrwert, keine Technik/UI)

Entwurf vorlegen, iterieren.

---

## Phase 6 – Dokumentation

**`docs/explanation/product.md`:** Nutzergruppen und Begriffe erweitern (nicht entfernen).

**`docs/features/<slug>.md`:** Feature-Spezifikation mit Motivation, Nutzergruppen, Beschreibung, Stories mit Akzeptanzkriterien.

**Commit:** `ai(docs): refine feature <name>`
