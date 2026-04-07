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

Dieser Skill führt durch einen strukturierten TDD-Prozess mit zwei expliziten Freigabe-Checkpoints
und vier Commit-Meilensteinen.

---

## Übersicht

```
Phase 1 – Fachliche Definition        (Nutzereingabe)
Phase 2 – Gherkin-Testfälle           (Agent schlägt vor → Freigabe 1)
Phase 3 – Architekturplanung          (Agent plant → Freigabe 2)
Phase 4 – Implementierung & Tests     (Nutzer entwickelt TDD-basiert)
Phase 5 – Visual Regression Tests     (Nutzer ergänzt Playwright VRT)
```

---

## Phase 1 – Fachliche Definition

Stillschweigend erkunden:
- Projektstruktur und verwendetes Framework (React/Angular/Vue etc.)
- Vorhandene Komponenten, Testinfrastruktur und Konventionen
- Playwright/Cucumber-Konfiguration, falls vorhanden

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

**Ausgabe:**

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
> „Hier sind die fachlichen Testfälle. Bitte bestätige oder gib Feedback:
> [Gherkin-Testfälle]"

Feedback einarbeiten und erneut vorlegen, bis explizite Freigabe erfolgt.
Jede positive Reaktion zählt (ja, ok, passt, lgtm). Schweigen zählt nicht.

**Commit 1:** Nutzer informieren:
> „Bitte committe jetzt die Gherkin-Testfälle (z.B. unter `e2e/features/<feature>.feature`).
> Gib Bescheid, wenn der Commit erfolgt ist."

Warten bis Nutzer den Commit bestätigt.

---

## Phase 3 – Architekturplanung

UI-Architekturplan erstellen. Framework-spezifische Konventionen aus Phase 1 berücksichtigen.

**Plan-Struktur:**

```
## Komponenten
- [KomponentenName] – [Verantwortlichkeit, 1 Satz]
  - Props: [Eingaben]
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
      [Komponente].test.tsx
      ...
```

**>>> FREIGABE 2:** Via `AskUserQuestion` vorlegen:
> „Hier ist der Architekturplan. Bitte bestätige oder gib Feedback:
> [Plan]"

Feedback einarbeiten und erneut vorlegen, bis explizite Freigabe erfolgt.

**Commit 2:** Nutzer informieren:
> „Bitte committe jetzt den Architekturplan (z.B. unter `docs/plans/YYYY-MM-DD-<feature>.md`).
> Gib Bescheid, wenn der Commit erfolgt ist."

Warten bis Nutzer den Commit bestätigt.

---

## Phase 4 – Implementierung & Unit/Component Tests

Der Nutzer implementiert das Feature TDD-basiert nach dem Architekturplan aus Phase 3.

**Hinweise für den Nutzer ausgeben:**

```
## Dein nächster Schritt: Implementierung

Entwickle das Feature TDD-basiert:
1. Schreibe zuerst Unit/Component-Tests (noch rot)
2. Implementiere die Komponente(n) schrittweise
3. Stelle sicher, dass alle Tests grün sind
4. Die Gherkin-Testfälle aus Phase 2 dienen als fachliche Referenz

Framework-Konventionen:
- [Aus Phase 1 erkannte Testbibliothek, z.B. Jest + React Testing Library / Jasmine + Karma]
- [Erkannte Mocking-Strategie]

Gib Bescheid, wenn die Implementierung abgeschlossen ist und alle Tests grün sind.
```

Warten bis Nutzer die Implementierung bestätigt.

**Commit 3:** Nutzer informieren:
> „Bitte committe jetzt den implementierten Code inkl. Unit/Component-Tests.
> Gib Bescheid, wenn der Commit erfolgt ist."

Warten bis Nutzer den Commit bestätigt.

---

## Phase 5 – Visual Regression Tests

**Hinweise für den Nutzer ausgeben:**

```
## Dein nächster Schritt: Visual Regression Tests

Ergänze Playwright Visual Regression Tests für die neue UI:
1. Screenshot-Tests für die Hauptzustände der Komponente(n)
2. Wichtige Interaktionszustände (hover, focus, error, loading)
3. Responsive Breakpoints falls relevant

Beispiel-Struktur:
  test('zeigt Komponente korrekt an', async ({ page }) => {
    await page.goto('/...');
    await expect(page).toHaveScreenshot('[feature]-default.png');
  });

Gib Bescheid, wenn die Visual Regression Tests hinzugefügt wurden.
```

Warten bis Nutzer die Visual Regression Tests bestätigt.

**Commit 4:** Nutzer informieren:
> „Bitte committe jetzt die Visual Regression Tests.
> Gib Bescheid, wenn der Commit erfolgt ist."

Warten bis Nutzer den Commit bestätigt.

---

## Abschluss

Zusammenfassung ausgeben:

```
## Feature abgeschlossen ✓

Commit 1 – Gherkin-Testfälle
Commit 2 – Architekturplan
Commit 3 – Implementierung & Unit/Component Tests
Commit 4 – Visual Regression Tests

Nächste Schritte (optional):
- E2E-Tests gegen laufende Anwendung ausführen
- Pull Request erstellen
```
