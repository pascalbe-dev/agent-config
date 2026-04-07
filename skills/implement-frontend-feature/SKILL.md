---
name: implement-frontend-feature
description: >-
  TDD-basierte Implementierung eines Features in einem Frontend-Projekt (React, Angular, Vue, etc.).
  Setzt eine vorhandene fachliche Feature-Spezifikation voraus.
  Trigger: "Implementiere Feature X im Frontend", "Baue Komponente Y", "Setze UI-Feature Z um".
  NUR für Frontend-Projekte – nicht für Backend-Entwicklung verwenden.
---

# Frontend Feature implementieren (TDD)

**Nur für Frontend-Projekte. Sprache: Immer die des Nutzers.**

**Vorbedingung:** Eine fachliche Feature-Spezifikation muss vorhanden sein (z.B. unter `docs/features/<feature>.md`). Falls nicht gefunden: Nutzer via `AskUserQuestion` nach dem Pfad fragen.

**Commit-Regel:** Nach jedem Teilarbeitsschritt committen – auch innerhalb einer Phase bei Feedback-Iterationen.

---

## Phase 1 – Gherkin-Testfälle

Codebase und Feature-Spezifikation lesen. Playwright/Cucumber-Testfälle im Gherkin-Format entwerfen: Happy Path + relevante Fehlerfälle, rein fachlich aus Nutzerperspektive.

**>>> FREIGABE via `AskUserQuestion`.** Feedback einarbeiten und committen, bis explizite Zustimmung erfolgt.

Commit: `test: add gherkin scenarios for <feature>`

---

## Phase 2 – Architekturplan

UI-Architekturplan erstellen: Komponenten, Datenfluss, State-Management, Schnittstellen, geplante Dateistruktur. Framework-Konventionen berücksichtigen.

**>>> FREIGABE via `AskUserQuestion`.** Feedback einarbeiten und committen, bis explizite Zustimmung erfolgt.

Commit: `docs: add architecture plan for <feature>`

---

## Phase 3 – Implementierung

Feature nach Plan implementieren. Keine Unit-Tests – die Gherkin-Tests aus Phase 1 sind die Verifikationsschicht.

Nach jedem sinnvollen Teilschritt committen: `feat: <was wurde gemacht>`

Am Ende Gherkin-Tests ausführen – alle müssen grün sein.

Bei ungeplanten Abweichungen mit hohem Impact (Routing, State-Management, API-Schnittstelle): `AskUserQuestion` vor der Umsetzung.

---

## Phase 4 – Visual Regression Tests

Playwright Snapshot-Tests (`toHaveScreenshot()`) schreiben: Hauptzustand + relevante Interaktionszustände (error, loading, disabled etc.).

Tests ausführen, Snapshots generieren, alle Tests grün.

Commit: `test: add visual regression tests for <feature>`
