---
name: refine-feature
description: >-
  Verwenden wenn der Nutzer ein Feature verfeinern, spezifizieren oder besprechen möchte.
  Trigger: "Lass uns Feature X verfeinern", "Ich habe eine Idee für Feature Y",
  "Können wir Feature Z besprechen". Funktioniert für alle Produkttypen (Frontend,
  Backend, Mobile, etc.).
---

# Feature Refinement

Ergebnis dieses Skills:
1. `docs/explanation/product.md` – aktualisiert mit neuen Begriffen und Nutzergruppen
2. `docs/features/<feature-slug>.md` – Feature-Spezifikation mit Akzeptanzkriterien

**Sprache:** Immer in der Sprache des Nutzers kommunizieren.

---

## Vorbereitung

**Vor dem ersten Satz an den Nutzer** stillschweigend lesen:
- `docs/explanation/product.md` – Produktbeschreibung, Nutzergruppen, Glossar
- Alle Dateien in `docs/features/` – bestehende Features

Kurze Zusammenfassung ausgeben:
```
📚 Dokumentation geladen:
- Produktdoku: [gefunden / nicht vorhanden]
- Bekannte Begriffe: [N]
- Nutzergruppen: [Liste oder "keine"]
- Bestehende Features: [Liste oder "keine"]
```

Danach direkt mit Phase 1 beginnen.

---

## Phase 1 – Feature verstehen

Klärende Fragen stellen (eine nach der anderen, kein Code/Technik):
- Welches Problem löst das Feature?
- Wer profitiert davon und wie?
- Welches Verhalten erwartet der Nutzer?
- Gibt es Einschränkungen oder Randfälle?

Abschluss: Feature in 3–5 Sätzen zusammenfassen, Bestätigung abwarten.

---

## Phase 2 – Begriffe & Ubiquitous Language

Fachbegriffe aus Phase 1 identifizieren. Bereits definierte Begriffe aus `product.md` nicht neu definieren.

Für jeden neuen Begriff:
- Deutschen Begriff (für Diskussion) und englischen Begriff (für Code) vorschlagen
- Definition vorschlagen, Bestätigung einholen

Ausgabe nach Bestätigung:
```markdown
| Deutsch | Englisch (Code) | Definition |
|---------|----------------|-----------|
| ...     | ...            | ...       |
```

---

## Phase 3 – Nutzergruppen

Bestehende Nutzergruppen aus `product.md` verwenden. Klären welche Gruppen betroffen sind und ob neue eingeführt werden müssen. Für neue Gruppen: Rolle und Ziele klären.

---

## Phase 4 – Akzeptanzkriterien

3–8 testbare Kriterien definieren. Format:
- „Gegeben [Vorbedingung], wenn [Aktion], dann [Ergebnis]"
- Oder einfache Bullet-Points für UI/UX-Kriterien

Entwurf vorlegen, iterieren bis Bestätigung.

---

## Phase 5 – Dokumentation schreiben

### `docs/explanation/product.md`

Bestehenden Inhalt erweitern – **niemals Einträge entfernen**. Alphabetische Reihenfolge im Glossar. Falls Datei nicht existiert: Nutzer nach Produktname und Kurzbeschreibung fragen.

```markdown
# [Produktname]

## Beschreibung
[Kurzbeschreibung]

## Nutzergruppen

### [Gruppenname]
[Beschreibung, Rolle, Ziele]

## Ubiquitäre Sprache

| Deutsch | Englisch (Code) | Definition |
|---------|----------------|-----------|
| ...     | ...            | ...       |
```

### `docs/features/<feature-slug>.md`

Slug: Kleinbuchstaben, Bindestriche, keine Sonderzeichen. Beispiel: „Benutzer Benachrichtigung" → `benutzer-benachrichtigung`

```markdown
# Feature: [Name]

## Motivation
[Problem, Grund, Auslöser]

## Nutzergruppen
[Betroffene Gruppen und ihre Rolle]

## Beschreibung
[Feature aus Nutzerperspektive, keine Implementierungsdetails]

## Akzeptanzkriterien
- Gegeben [X], wenn [Y], dann [Z]

## Offene Fragen
[Ungeklärte Punkte – leer lassen falls keine]
```

### Commit

Commit nach [commit skill](./../commit/SKILL.md) mit Typ `ai(docs)`:
```
ai(docs): refine feature <feature-name>
```

Abschließende Ausgabe:
```
✅ Refinement abgeschlossen!
📄 docs/explanation/product.md aktualisiert
📄 docs/features/<feature-slug>.md erstellt
```
