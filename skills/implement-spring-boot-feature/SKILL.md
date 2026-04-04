---
name: implement-spring-boot-feature
description: >-
  Verwenden wenn ein bereits verfeinertes Feature in einem Spring Boot-Projekt umgesetzt werden soll.
  Trigger: "Setze Feature X um", "Implementiere Feature Y", "Bauen wir Feature Z".
  Setzt eine Feature-Spezifikation unter docs/features/<slug>.md voraus (erstellt via refine-feature).
---

# Spring Boot Feature implementieren

**Vorbedingungen:** Git-Repo mit Remote konfiguriert. Feature-Spezifikation unter `docs/features/<slug>.md` vorhanden. Fehlende Verzeichnisse (`docs/plans/`, `docs/decisions/`) werden automatisch angelegt.

**Sprache:** Immer in der Sprache des Nutzers kommunizieren.

Nach jeder Phase Kontext-Status ausgeben:
```
📊 Kontext: [████████░░] ~80% — Phase N abgeschlossen
```
Bei >80%: Zusammenfassung der bisherigen Erkenntnisse ausgeben, bevor weitergemacht wird.

---

## Phase 1 – Exploration

Zuerst lesen: `docs/explanation/service.md` und `docs/features/<slug>.md`.

Codebase erkunden und Ergebnis ausgeben:
```
## Architektur-Kontext
## Relevante Dateien
## Bestehende Patterns
## Offene technische Fragen
```

Falls Feature bereits teilweise implementiert: Nutzer informieren, Plan anpassen.

Offene technische Fragen einzeln via `AskUserQuestion` klären.

---

## Phase 2 – Integrationstest

### 2a – Planung

Integrationstest-Plan entwerfen (End-to-End, Happy Path):
- `@SpringBootTest` für Spring-Kontext
- TestContainers für externe Abhängigkeiten (DB, Message Broker, etc.)

```
## Integrationstest: [Feature-Name]
**Ziel:** Was validiert wird
**Setup:** Benötigte TestContainers, Testdaten
**Szenarien:** Liste der Testfälle
**Assertions:** Was geprüft wird
```

Plan via `AskUserQuestion` vorlegen. Nach Freigabe unter `docs/plans/YYYY-MM-DD-<feature>-integration-test.md` speichern.

### 2b – Implementierung

1. Testklasse(n) in `src/test/java/...` anlegen
2. TestContainers konfigurieren
3. `@SpringBootTest` mit passendem `webEnvironment` aufsetzen
4. Szenarien und Assertions implementieren
5. Test ausführen (darf bei noch nicht implementiertem Feature fehlschlagen)

Commit nach [commit skill](./../commit/SKILL.md) mit Typ `test`.

---

## Phase 3 – Feature-Planung

Plan erstellen und via `AskUserQuestion` vorlegen. Feedback einarbeiten bis zur Freigabe.

Architektur: Hexagonal Architecture – [hexagonal-arch skill](./../hexagonal-arch/SKILL.md) vor der Planung lesen.

```
## Schritt N: [Titel]
**Layer:** Domain / Application / Adapter-In / Adapter-Out / Infrastruktur
**Was passiert:** 1–2 Sätze
```

Freigabe: Jede positive Reaktion zählt (ja, ok, passt, lgtm). Schweigen zählt nicht.

Plan unter `docs/plans/YYYY-MM-DD-<feature>.md` speichern.

---

## Phase 4 – Umsetzung

Schritte sequenziell abarbeiten. [hexagonal-arch skill](./../hexagonal-arch/SKILL.md) als Architektur-Referenz nutzen.

Keine Rückfragen bei normalen Implementierungsentscheidungen.

| Impact | Vorgehen |
|--------|----------|
| Hoch | Stoppen → `AskUserQuestion` → nach Antwort weitermachen |
| Niedrig | Selbst entscheiden + in `docs/decisions/YYYY-MM-DD-<feature>.md` dokumentieren |

Hoch: ungeplante DB-Schema-Änderung, sync/async-Entscheidung, Sicherheitsrelevanz
Niedrig: Variablennamen, interne Implementierungsdetails, Log-Format

Nach jedem Schritt: Commit nach [commit skill](./../commit/SKILL.md).

---

## Phase 5 – Verifikation

Sequenziell, Fehler stoppt Folgeschritte:
1. Tests → bei Fehler: einmal selbst beheben; sonst Nutzer informieren
2. Linting → auto-fix
3. Formatierung → auto-fix
4. Build → bei Fehler: Nutzer mit genauem Fehler informieren

---

## Phase 6 – Doku & PR

`docs/explanation/service.md` prüfen: Was hat sich durch Phase 4 geändert? Veraltetes aktualisieren, Stil beibehalten. Falls nichts geändert: „Keine Änderungen nötig". Nur diese Datei anpassen.

Bei Änderungen: Commit mit Typ `ai(docs)` nach [commit skill](./../commit/SKILL.md).

PR erstellen:
```
Title: feat(scope): Kurzbeschreibung

## Summary
- Schritt 1: was gemacht wurde
- Schritt 2: was gemacht wurde

## Plan
Siehe: docs/plans/YYYY-MM-DD-<feature>.md

## Decisions
[nur falls vorhanden] Siehe: docs/decisions/YYYY-MM-DD-<feature>.md

🤖 Generated with Claude Code
```

---

## Format: Decisions-Datei

`docs/decisions/YYYY-MM-DD-<feature>.md`:
```markdown
# Decisions: <Feature-Name>

## Schritt N – <Titel>
**Entscheidung:** Was entschieden wurde
**Grund:** Warum
```
