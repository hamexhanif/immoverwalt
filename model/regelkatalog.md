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

---

## REGEL-04

- **Regel:** Ist eine Mieteinheit angegeben, wird das zugehörige Objekt aus dem Gebäude der Einheit übernommen und ein zuvor angegebenes Objekt überschrieben.
- **Bereich:** Meldungen
- **Fundstellen:**
  - `src/Service/IssueService.php::createIssue()`
  - `src/Controller/IssueController.php::new()`
- **Gegenbeobachtung:** Das Formular erlaubt die Auswahl von Objekt und Mieteinheit unabhängig voneinander; die Zuordnung wird erst beim Speichern anhand der Einheit korrigiert.
- **Status:** bestätigt
- **Offene Frage:** -

---

## REGEL-05

- **Regel:** Ist bei einer Meldung zu einer Mieteinheit kein meldender Mieter angegeben, wird der erste Mieter des ersten aktiven Mietverhältnisses dieser Einheit übernommen, sofern ein aktives Mietverhältnis mit Mietern gefunden wird. Wird kein Mieter gefunden, kann die Meldung trotzdem ohne Mieter angelegt werden.
- **Bereich:** Meldungen
- **Fundstellen:**
  - `src/Service/IssueService.php::createIssue()`
  - `src/Form/IssueType.php::Feld tenant`
- **Gegenbeobachtung:** Keine Fehlermeldung, Ausnahme oder Warnung, wenn kein aktives Mietverhältnis oder kein Mieter gefunden wird.
- **Status:** zu klären
- **Offene Frage:** Soll beim Versuch, eine Meldung für eine Mieteinheit ohne zugeordneten Mieter anzulegen, ein Fehler, eine Ausnahme oder eine Warnung ausgegeben werden?

---

## REGEL-06

- **Regel:** Fehlt eine zuständige Person, wird beim Erfassen der aktuell angemeldete Benutzer als zuständig gesetzt.
- **Bereich:** Meldungen
- **Fundstellen:**
  - `src/Service/IssueService.php::createIssue()`
  - `src/Form/IssueType.php::Feld assignee`
- **Gegenbeobachtung:** -
- **Status:** bestätigt
- **Offene Frage:** -

---

## REGEL-07

- **Regel:** Beim Erfassen einer Meldung wird eine Meldungsnummer im Format ME- plus fünfstelliger Nummer vergeben. Die Nummer wird aus der aktuellen Anzahl gespeicherter Meldungen berechnet: `2000 + Anzahl + 1`.
- **Bereich:** Meldungen
- **Fundstellen:**
  - `src/Service/IssueService.php::createIssue()`
  - `src/Repository/IssueRepository.php::nextNumber()`
- **Gegenbeobachtung:** Die Nummer basiert auf der Anzahl vorhandener Meldungen und nicht auf einer fortlaufenden Sequenz. Die Datenbank verlangt zugleich Eindeutigkeit. Verhalten bei paralleler Erstellung oder gelöschten Meldungen ist nicht abgesichert.
- **Status:** zu klären
- **Offene Frage:** Soll die Nummerierung dauerhaft fortlaufend und gegen parallele Vergabe abgesichert sein?

---

## REGEL-08

- **Regel:** Eine Meldung mit Priorität 4 oder höher wird beim Erfassen direkt auf IN_REVIEW gesetzt.
- **Bereich:** Meldungen
- **Fundstellen:**
  - `src/Service/IssueService.php::createIssue()`
  - `src/Entity/Issue.php::PRIORITIES, $status`
- **Gegenbeobachtung:** Das Formular bietet nur Prioritäten 1 bis 4 an; die Regel verwendet aber >= 4. Für niedrigere Prioritäten bleibt der Standardstatus NEW.
- **Status:** zu klären
- **Offene Frage:** Soll die automatische Prüfung ausschließlich für die auswählbare Priorität „dringend“ gelten?

---

## REGEL-09

- **Regel:** Beim Erfassen wird ein Statusverlaufseintrag mit dem bisherigen Status `null`, dem aktuellen Meldungsstatus, dem Benutzernamen des erfassenden Benutzers und dem Kommentar „Meldung erfasst“ angelegt.
- **Bereich:** Meldungen
- **Fundstellen:**
  - `src/Service/IssueService.php::createIssue()`
  - `src/Entity/IssueStatusChange.php::Konstruktor`
- **Gegenbeobachtung:** Nicht alle späteren Statusänderungswege legen ebenfalls einen Verlaufseintrag an; siehe REGEL-10.
- **Status:** bestätigt
- **Offene Frage:** -

---

## REGEL-10

- **Regel:** Eine Statusänderung über das Status-Auswahlfeld der Detailansicht wird nur verarbeitet, wenn ein neuer, nicht-leerer Status eingegeben wurde und dieser vom bisherigen Status abweicht. Dabei wird ein Verlaufseintrag mit Benutzer und Kommentar gespeichert.
- **Bereich:** Meldungen
- **Fundstellen:**
  - `src/Controller/IssueController.php::status()`
  - `src/Entity/Issue.php::STATUSES`
- **Gegenbeobachtung:** Der eingegebene Status wird nicht gegen die erlaubten Statuswerte validiert. Beim Schließen (`IssueService::closeIssue()`), bei der Sammeländerung (`IssueController::bulk()`) und beim automatischen Wechsel auf `IN_PROGRESS` nach dem Anlegen einer Aufgabe (`IssueService::createTaskFromIssue()`) wird zwar der Meldungsstatus geändert, aber kein Verlaufseintrag angelegt. Die Detailansicht zeigt ausschließlich gespeicherte Verlaufseinträge an.
- **Status:** widersprüchlich
- **Offene Frage:** Soll jede Statusänderung – einschließlich Schließen, Sammeländerung und automatischer Wechsel – mit altem Status, neuem Status, Zeitpunkt und auslösendem Benutzer in der Historie erfasst werden?

---

## REGEL-11

- **Regel:** Wird aus einer Meldung eine Aufgabe erstellt, wechselt die Meldung von `NEW` oder `IN_REVIEW` auf `IN_PROGRESS`. Bei anderen Meldungsstatus bleibt der Status unverändert.
- **Bereich:** Meldungen/Aufgaben
- **Fundstellen:**
  - `src/Service/IssueService.php::createTaskFromIssue()`
- **Gegenbeobachtung:** Der automatische Statuswechsel erzeugt an dieser Stelle keinen Statusverlaufseintrag.
- **Status:** zu klären
- **Offene Frage:** Soll der automatische Statuswechsel ebenfalls in der Historie protokolliert werden?

---

## REGEL-12

- **Regel:** Für Meldungen der Kategorien Heizung, Sanitär, Elektro und Aufzug wird jeweils ein Dienstleister mit passendem Gewerk gesucht. Wird einer gefunden, werden Dienstleister und Kontaktperson übernommen und der Aufgabenstatus auf `ASSIGNED` gesetzt.
- **Bereich:** Meldungen/Aufgaben
- **Fundstellen:**
  - `src/Service/IssueService.php::createTaskFromIssue()`
  - `src/Entity/Issue.php::CATEGORIES`
- **Gegenbeobachtung:** Für Fenster, Schimmel, Beschwerden und Sonstiges gibt es keine Zuordnung in der Abbildung. Wird kein passender Dienstleister gefunden, setzt dieser Code weder Dienstleister noch Aufgabenstatus.
- **Status:** zu klären
- **Offene Frage:** Soll es auch für die übrigen Kategorien eine Dienstleisterzuordnung geben?

---

## REGEL-13

- **Regel:** Die Meldungsliste zeigt standardmäßig offene Meldungen. „Offen“ bedeutet dort: Status ist weder `CLOSED` noch `REJECTED`. Der Filter „alle“ schränkt den Status nicht ein; ein Statusfilter zeigt genau diesen Status.
- **Bereich:** Meldungen
- **Fundstellen:**
  - `src/Controller/IssueController.php::index()`
  - `src/Repository/IssueRepository.php::findByStatusFilter()`
- **Gegenbeobachtung:** `RESOLVED` zählt in dieser Listenfilterung weiterhin als offen.
- **Status:** zu klären
- **Offene Frage:** Soll „offen“ auch erledigte (`RESOLVED`) Meldungen ausschließen?

---

## REGEL-14

- **Regel:** Die Zählmethode für offene Meldungen zählt nur Meldungen, deren Status weder `RESOLVED`, `CLOSED` noch `REJECTED` ist.
- **Bereich:** Meldungen
- **Fundstellen:**
  - `src/Repository/IssueRepository.php::countOpen()`
  - `src/Repository/IssueRepository.php::findByStatusFilter()`
- **Gegenbeobachtung:** Das weicht vom Listenfilter „offen“ ab, der `RESOLVED` nicht ausschließt.
- **Status:** widersprüchlich
- **Offene Frage:** Welche Definition von „offen“ ist fachlich maßgeblich?

---

## REGEL-15

- **Regel:** 
- **Bereich:** 
- **Fundstellen:**
  - `xxx`
  - `xxx`
- **Gegenbeobachtung:** 
- **Status:** 
- **Offene Frage:** 

---

## REGEL-16

- **Regel:** 
- **Bereich:** 
- **Fundstellen:**
  - `xxx`
  - `xxx`
- **Gegenbeobachtung:** 
- **Status:** 
- **Offene Frage:** 

---

## REGEL-17

- **Regel:** 
- **Bereich:** 
- **Fundstellen:**
  - `xxx`
  - `xxx`
- **Gegenbeobachtung:** 
- **Status:** 
- **Offene Frage:** 