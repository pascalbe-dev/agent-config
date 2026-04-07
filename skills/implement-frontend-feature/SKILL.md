---
name: implement-frontend-feature
description: >-
  Verwenden wenn ein neues Feature in einem Frontend-Projekt (React, Angular, etc.) TDD-basiert
  umgesetzt werden soll. Führt durch den vollständigen Prozess von der fachlichen Spezifikation
  über Gherkin-Tests und Architekturplanung bis zu Visual Regression Tests.
  Trigger: "Implementiere Feature X im Frontend", "Baue Komponente Y", "Setze UI-Feature Z um".
  NUR für Frontend-Projekte (React, Angular, Vue, etc.) – nicht für Backend-Entwicklung verwenden.
---

# Frontend Feature implementieren (TDD)

**Nur für Frontend-Projekte (React, Angular, Vue, etc.).**

**Sprache:** Immer in der Sprache des Nutzers kommunizieren.

Dieser Skill führt durch einen strukturierten Prozess mit zwei expliziten Freigabe-Checkpoints.
Der Agent übernimmt alle Phasen vollständig. Nach jedem abgeschlossenen Teilarbeitsschritt
wird committet – auch innerhalb einer Phase, wenn Feedback eingearbeitet wurde.

**Commit-Regel:** Jeder abgeschlossene Teilarbeitsschritt = ein Commit. Kein Teilschritt bleibt
uncommittet.

---

## Übersicht

```
Phase 1 – Fachliche Definition     (Agent fragt, Nutzer antwortet)
Phase 2 – Gherkin-Testfälle        (Agent erstellt → Freigabe 1 → Agent committet)
Phase 3 – Architekturplanung       (Agent plant → Freigabe 2 → Agent committet)
Phase 4 – Implementierung          (Agent implementiert → Agent committet)
Phase 5 – Visual Regression Tests  (Agent schreibt Tests → Agent committet)
```

---

## Phase 1 – Fachliche Definition

Stillschweigend erkunden:
- Projektstruktur und verwendetes Framework (React/Angular/Vue etc.)
- Vorhandene Komponenten, Playwright/Cucumber-Konfiguration und Testkonventionen
- Bestehende Coding-Konventionen und Dateistruktur

Folgende Fragen einzeln via `AskUserQuestion` stellen:

1. Beschreibe das Feature: Was soll der Nutzer tun können?
2. Welche Nutzergruppen sind betroffen?
3. Gibt es bekannte Randfälle oder Einschränkungen?

Feature kurz zusammenfassen und Bestätigung abwarten, bevor weitergemacht wird.

---

## Phase 2 – Gherkin-Testfälle

Auf Basis von Phase 1 fachliche Playwright/Cucumber-Testfälle im Gherkin-Format entwerfen.

**Regeln:**
- Beschreibung aus Nutzerperspektive (kein technisches Jargon)
- Happy Path + wichtige Fehlerfälle
- Jedes Akzeptanzkriterium aus Phase 1 muss durch mindestens einen Testfall abgedeckt sein
- Noch keine Implementierungsdetails – rein fachlich

**Ausgabe-Format:**

```gherkin
Feature: [Feature-Name]
  Als [Nutzergruppe]
  Möchte ich [Aktion]
  Damit [Nutzen]

  Scenario: [Beschreibung Happy Path]
    Given [Ausgangszustand]
    When [Nutzeraktion]
    Then [Erwartetes Ergebnis]

  Scenario: [Beschreibung Fehlerfall]
    Given [Ausgangszustand]
    When [Fehlerhafte Aktion]
    Then [Erwartete Fehlermeldung/Reaktion]
```

**>>> FREIGABE 1:** Via `AskUserQuestion` vorlegen:
> „Hier sind die fachlichen Testfälle. Bitte bestätige oder gib Feedback."

Feedback einarbeiten, dann sofort committen und erneut vorlegen – so lange bis explizite
Freigabe erfolgt. Jede positive Reaktion zählt (ja, ok, passt, lgtm). Schweigen zählt nicht.

**Commit nach Freigabe:** Testfälle in passendem Pfad ablegen (z.B. `e2e/features/<feature>.feature`)
und committen: `test: add gherkin scenarios for <feature>`.

---

## Phase 3 – Architekturplanung

UI-Architekturplan erstellen. Framework-spezifische Konventionen aus Phase 1 berücksichtigen.

**Plan-Struktur:**

```
## Komponenten
- [KomponentenName] – [Verantwortlichkeit, 1 Satz]
  - Props/Inputs: [Eingaben]
  - Events/Outputs: [Ausgaben]

## Datenfluss
[Beschreibung: Woher kommen die Daten? Wie fließen sie durch die Komponenten?]

## State-Management
[Lokaler State / Context / Store – Begründung]

## Schnittstellen
[API-Aufrufe, Services, externe Abhängigkeiten]

## Dateistruktur (geplant)
src/
  components/
    [Feature]/
      [Komponente].tsx
      ...
```

**>>> FREIGABE 2:** Via `AskUserQuestion` vorlegen:
> „Hier ist der Architekturplan. Bitte bestätige oder gib Feedback."

Feedback einarbeiten, dann sofort committen und erneut vorlegen – so lange bis explizite
Freigabe erfolgt.

**Commit nach Freigabe:** Plan unter `docs/plans/YYYY-MM-DD-<feature>.md` ablegen und
committen: `docs: add architecture plan for <feature>`.

---

## Phase 4 – Implementierung

Der Agent implementiert das Feature nach dem Architekturplan aus Phase 3.

**Wichtig:** Keine Unit- oder Component-Tests in dieser Phase. Die Korrektheit wird durch
die Gherkin-Testfälle aus Phase 2 sichergestellt, die das Feature aus Nutzerperspektive
vollständig abdecken.

**Vorgehen:**
1. Komponenten schrittweise nach Plan implementieren
2. Nach jedem sinnvollen Teilschritt (Komponente, Service, Routing etc.) committen:
   `feat: implement <was wurde gemacht>`
3. Am Ende alle Gherkin-Tests ausführen und sicherstellen, dass sie grün sind

**Bei Abweichungen vom Plan:**

| Impact | Vorgehen |
|--------|----------|
| Hoch | Stoppen → `AskUserQuestion` → nach Antwort weitermachen und committen |
| Niedrig | Selbst entscheiden, im Commit-Message kurz begründen |

Hoch: ungeplante Änderung an Routing/State-Management/API-Schnittstelle, Sicherheitsrelevanz
Niedrig: interne Strukturdetails, Variablennamen, Hilfsfunktionen

---

## Phase 5 – Visual Regression Tests

Der Agent schreibt Playwright Visual Regression Tests, die dauerhaft sicherstellen, dass
die UI so aussieht, wie sie soll.

**Pflicht-Coverage:**
- Hauptzustand der Komponente(n) im Normalbetrieb
- Relevante Interaktionszustände (hover, focus, disabled, error, loading – soweit vorhanden)
- Responsive Breakpoints falls das Feature layout-sensitiv ist

**Test-Struktur:**

```typescript
test.describe('[Feature-Name] Visual Regression', () => {
  test('default state', async ({ page }) => {
    await page.goto('/[route]');
    await expect(page.locator('[data-testid="[komponente]"]')).toHaveScreenshot('[feature]-default.png');
  });

  test('error state', async ({ page }) => {
    // Setup für Fehlerzustand
    await expect(page.locator('[data-testid="[komponente]"]')).toHaveScreenshot('[feature]-error.png');
  });
});
```

Tests ausführen und sicherstellen, dass alle Snapshots generiert werden und die Tests grün sind.

**Commit nach Abschluss:** `test: add visual regression tests for <feature>`

Falls während der Test-Implementierung Feedback eingeholt wird (z.B. welche Zustände relevant sind):
Feedback einarbeiten, committen, dann weitermachen.

---

## Abschluss

Zusammenfassung ausgeben:

```
## Feature abgeschlossen

Abgeschlossene Phasen:
- Gherkin-Testfälle (Phase 2)
- Architekturplan (Phase 3)
- Implementierung (Phase 4)
- Visual Regression Tests (Phase 5)

Alle Tests grün. Die UI ist durch Playwright-Tests dauerhaft abgesichert.
```
