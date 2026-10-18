# Regelkatalog ImmoVerwalt

Fachliche Regeln des Altsystems – rekonstruiert aus dem Code.
Dieser Katalog ist die erste Fassung des Fachmodells und wird im Semester weiterentwickelt
(Fachmodell → Zustandsmodell → DSL → API-Spezifikation).

**Regeln formulieren:** als deutscher Satz, ohne technische Begriffe (kein Controller, kein Template, keine Tabelle).

**Status:** `bestätigt` · `widersprüchlich` · `zu klären`

| Feld | Bedeutung |
|---|---|
| Regel | fachliche Aussage in einem Satz |
| Bereich | Bestand · Vermietung · Meldungen/Aufgaben · Termine · Abrechnung |
| Fundstellen | Datei und Methode bzw. Template, an denen die Regel umgesetzt ist |
| Gegenbeobachtung | Stellen, an denen die Regel fehlt, umgangen wird oder anders umgesetzt ist |
| Status | siehe oben |
| Offene Frage | was mit dem Fachbereich zu klären ist |

---

## REGEL-01

- **Regel:** Eine Mieteinheit darf nur vermietet werden, wenn sie verfügbar ist und kein aktives Mietverhältnis besitzt.
- **Bereich:** Vermietung
- **Fundstellen:**
  - `templates/unit/show.html.twig` – Button »Mietvertrag anlegen« nur bei Status `AVAILABLE`
  - `src/Controller/TenancyController.php::new()` – prüft den Status der Einheit
  - `src/Service/TenancyService.php::createTenancy()` – prüft vorhandene aktive Mietverhältnisse
  - `src/Repository/TenancyRepository.php::findActiveForUnit()` – Abfrage der aktiven Verträge
- **Gegenbeobachtung:** Der Controller lässt auch reservierte Einheiten zu, das Template bietet den Vertrag nur bei freien Einheiten an.
- **Status:** widersprüchlich
- **Offene Frage:** Darf für eine reservierte Einheit ein Vertrag angelegt werden – und wer darf reservieren?

---

## REGEL-02

- **Regel:** Eine Meldung kann nur geschlossen werden, nur wenn sie keine offenen Aufgaben übrig hat.
- **Bereich:** Meldungen/Aufgaben
- **Fundstellen:**
  - `src/Service/IssueService.php::closeIssue()`
  - `src/Controller/IssueController.php::close()`
  - `templates/issue/show.html.twig::Meldung schließen-Button`
- **Gegenbeobachtung:** Die Regel gilt nur über den Button “Meldung schließen”. Über das Status-Auswahlfeld (Controller::status()) und die Sammeländerung (Controller::bulk()) lässt sich der Status “Geschlossen” ohne Prüfung setzen.
- **Status:** widersprüchlich
- **Offene Frage:** Soll die Regel auch für Statuswechsel und Sammeländerung gelten?

---

## REGEL-03

- **Regel:** Eine neue Aufgabe ist bei Priorität 3 oder höher in 2 Tagen fällig, sonst in 14 Tagen.
- **Bereich:** Aufgaben
- **Fundstellen:**
  - `src/Service/IssueService.php::createTaskFromIssue()`
  - `templates/issue/show.html.twig::Aufgaben fällig Spalte`
- **Gegenbeobachtung:** - 
- **Status:** zu klären
- **Offene Frage:** Sind hier Kalendertage oder Werktage gemeint?
