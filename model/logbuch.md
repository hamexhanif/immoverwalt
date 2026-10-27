# Logbuch der Transformation

Projektgruppe: _Namen (2–3 Personen)_

Hier halten Sie Ihren Weg fest – nicht nur das Ergebnis. In der mündlichen Prüfung wird die Transformation
bewertet, vor allem der Weg dorthin: Welche Entscheidungen haben Sie getroffen, warum, und was haben Sie
verworfen? Schreiben Sie nach jeder Übung und jeder Arbeitssitzung einen kurzen Eintrag. Größere
Architekturentscheidungen dokumentieren Sie zusätzlich als ADR in `model/adr/`.

## Vorlage für einen Eintrag

```markdown
## JJJJ-MM-TT – Thema (z. B. Übung 3: Aggregate)

**Was wir gemacht haben:** …
**Entscheidung:** … **Begründung:** …
**Verworfen:** … (und warum)
**KI-Einsatz:** Wofür, mit welchem Auftrag? Was haben wir übernommen, was korrigiert?
**Offene Fragen:** …
**Artefakte:** z. B. model/fachmodell.md, Commit abc1234
```

## Einträge

## 2026-10-13 – Übung 2: Regeln

**Was wir gemacht haben:** Paare Regeln für Meldungen extrahieren
**Entscheidung:** REGEL-02 und REGEL-03 hinzufügen **Begründung:** -
**Verworfen:** -
**KI-Einsatz:** -
**Offene Fragen:** -
**Artefakte:** `model/regelkatalog.md`

## 2026-10-27 – Übung 2: Regeln (cont.)

**Was wir gemacht haben:** Paare Regeln für Meldungen vervollständigen
**Entscheidung:** REGEL-04 bis 14 hinzufügen **Begründung:** Die Regel ist noch zu wenig
**Verworfen:** -
**KI-Einsatz:** Copilot für Vorschläge für mögliche zusätzliche Regeln; anschließend die Regeln manuell testen und ebenfalls den Code  manuell analysieren.
**Offene Fragen:** Die Regeln sind nur noch für Meldungen, für andere Bereiche sind noch zu vervollständigen.
**Artefakte:** `model/regelkatalog.md`