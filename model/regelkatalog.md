# Regelkatalog ImmoVerwalt

Fachliche Regeln des Altsystems – rekonstruiert aus dem Code.
Dieser Katalog ist die erste Fassung des Fachmodells und wird im Semester weiterentwickelt
(Fachmodell → Zustandsmodell → DSL → API-Spezifikation).

**Regeln formulieren:** als deutscher Satz, ohne technische Begriffe (kein Controller, kein Template, keine Tabelle).
Begriffe nach `glossar.md`.

**Status:** `bestätigt` · `widersprüchlich` · `zu klären`

| Feld | Bedeutung |
|---|---|
| Regel | fachliche Aussage in einem Satz |
| Bereich | Bestand · Vermietung · Meldungen/Aufgaben · Termine · Abrechnung |
| Fundstellen | Datei und Methode bzw. Template, an denen die Regel umgesetzt ist |
| Gegenbeobachtung | Stellen, an denen die Regel fehlt, umgangen wird oder anders umgesetzt ist |
| Status | siehe oben |
| Offene Frage | was mit dem Fachbereich zu klären ist |

**Erweiterung in Übung 4 (Leitfaden Schritt 7)** – zusätzliche Felder:

| Feld | Bedeutung |
|---|---|
| Anwendungsfall | AF-Nummer aus `anwendungsfaelle.md` |
| Vorbedingung | muss gelten, **bevor** der Befehl ausgeführt wird – sonst wird er abgelehnt |
| Nachbedingung | gilt, **nachdem** der Befehl ausgeführt wurde |
| Invariante | gilt **immer** |
| Betroffene Objekte | Objekte aus `fachmodell.md`, die die Regel gleichzeitig prüft oder ändert |
| Aggregat | Aggregat, das die Regel einhält (Schritt 8) – oder »mehrere → Anwendungsschicht« |
| Entscheidung | bei Widerspruch im Altsystem: gewählte Lösung (Begründung im Logbuch `log.md`) |

So zerlegt, lässt sich jede Regel später in einen Test übersetzen.

**Stand:** Die Regeln der **Vermietung** (REGEL-01, REGEL-15 bis REGEL-29) sind im erweiterten Format.
REGEL-02 bis REGEL-14 (Meldungen/Aufgaben, Übung 2) werden erweitert, sobald für Meldungen/Aufgaben die Schritte 1–6
erledigt sind (Logbuch, offene Aufgaben).

## Übersicht Vermietung

| Regel | Kurzform | Art | Anwendungsfall | Aggregat | Status |
|---|---|---|---|---|---|
| REGEL-01 | Anlegen nur für freie oder eigens reservierte Mieteinheit | Vorbedingung | AF-05 | mehrere → Anwendungsschicht | widersprüchlich → entschieden |
| REGEL-15 | keine Überschneidung der Laufzeiten einer Mieteinheit | Invariante | AF-05 | mehrere → Anwendungsschicht | widersprüchlich → entschieden |
| REGEL-16 | Mietende nicht vor Mietbeginn | Invariante | AF-05, AF-08, AF-09, AF-10 | Mietverhältnis | widersprüchlich → entschieden |
| REGEL-17 | Vertragsnummer eindeutig | Nachbedingung, Invariante | AF-05 | Ablage | zu klären |
| REGEL-18 | Vorbelegung Kaltmiete, Nebenkostenvorauszahlung, Kaution | Nachbedingung | AF-05 | Mietverhältnis | zu klären |
| REGEL-19 | Zustand der Mieteinheit folgt dem Mietverhältnis | Nachbedingung (über Ereignis) | AF-03, AF-05, AF-07–AF-10 | mehrere → Reaktion auf Ereignisse | widersprüchlich → entschieden |
| REGEL-20 | Aktivieren | Vor- und Nachbedingung | AF-07 | Mietverhältnis | bestätigt |
| REGEL-21 | laufendes Mietverhältnis hat mindestens einen Mieter | Invariante | AF-06 | Mietverhältnis | widersprüchlich → entschieden |
| REGEL-22 | Mieterliste ändern | Vorbedingung, Invariante | AF-06 | Mietverhältnis | widersprüchlich → entschieden |
| REGEL-23 | Kündigung | Vor- und Nachbedingung | AF-08 | Mietverhältnis | widersprüchlich → entschieden |
| REGEL-24 | Aufhebung | Vor- und Nachbedingung | AF-09 | Mietverhältnis | widersprüchlich → entschieden |
| REGEL-25 | Einzug protokollieren | Vor- und Nachbedingung | AF-10 | Mietverhältnis | widersprüchlich → entschieden |
| REGEL-26 | Auszug protokollieren | Vor- und Nachbedingung | AF-10 | Mietverhältnis | widersprüchlich → entschieden |
| REGEL-27 | Zusage | Vor- und Nachbedingung | AF-03 | Interessent (Folgen über Ereignis) | widersprüchlich → entschieden |
| REGEL-28 | Besichtigung planen | Vor- und Nachbedingung | AF-02 | Interessent | widersprüchlich → entschieden |
| REGEL-29 | Zustandsfolge des Interessenten | Invariante | AF-01 | Interessent | widersprüchlich → entschieden |

»widersprüchlich → entschieden«: Das Altsystem ist widersprüchlich; für das Zielmodell ist entschieden (Feld
»Entscheidung«). Die Entscheidung bleibt mit dem Fachbereich zu bestätigen.

---

## REGEL-01

- **Regel:** Ein Mietverhältnis darf nur für eine Mieteinheit angelegt werden, die frei ist oder die für genau diesen Mieter (aus seiner Zusage) reserviert ist.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-05 Mietverhältnis anlegen
- **Vorbedingung:** Zustand der Mieteinheit = frei, **oder** Zustand = reserviert und die Reservierung gehört zu einem der Mieter des neuen Mietverhältnisses.
- **Nachbedingung:** – (siehe REGEL-19: die Einheit ist danach reserviert)
- **Invariante:** –
- **Betroffene Objekte:** Mieteinheit, Mietverhältnis, Mieter
- **Aggregat:** mehrere (Mieteinheit, Mietverhältnis) → Anwendungsschicht prüft vor dem Anlegen
- **Fundstellen:**
  - `templates/unit/show.html.twig` – Button »Mietvertrag anlegen« nur bei Status `AVAILABLE` (frei)
  - `src/Controller/TenancyController.php::new()` – lässt `AVAILABLE` und `RESERVED` zu
  - `src/Service/TenancyService.php::createTenancy()` – prüft vorhandene laufende Mietverhältnisse
  - `src/Repository/TenancyRepository.php::findActiveForUnit()` – laufend = aktiv oder gekündigt
- **Gegenbeobachtung:** Der Controller lässt auch reservierte Einheiten zu, das Template bietet den Vertrag nur bei freien Einheiten an. Der Weg über die Zusage (AF-03) braucht »reserviert« – dort wird die Einheit reserviert und sofort zur Vertragsanlage weitergeleitet. Das Altsystem merkt sich nicht, **für wen** reserviert ist; jeder kann die reservierte Einheit vermieten.
- **Status:** widersprüchlich
- **Entscheidung:** Alternative B aus VL 4 – auch reserviert, aber nur für die eigene Reservierung; sonst entstehen doppelte Verträge. Die Reservierung muss deshalb wissen, für wen sie gilt (E-20). Die Prüfung »kein laufendes Mietverhältnis« entfällt als eigene Regel, sie ist in REGEL-15 enthalten.
- **Offene Frage:** Darf während der Kündigungsfrist (Einheit »gekündigt«) schon das Mietverhältnis des Nachmieters angelegt werden? (F-12)

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

- **Regel:** Die Laufzeiten zweier Mietverhältnisse derselben Mieteinheit dürfen sich nicht überschneiden; Entwürfe zählen dabei nicht mit.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-05 Mietverhältnis anlegen
- **Vorbedingung:** Die Laufzeit des neuen Mietverhältnisses überschneidet sich mit keiner Laufzeit eines anderen Mietverhältnisses derselben Mieteinheit, das nicht Entwurf ist. Ein fehlendes Mietende gilt als »unbegrenzt«.
- **Nachbedingung:** –
- **Invariante:** Für jede Mieteinheit und jeden Tag gibt es höchstens ein Mietverhältnis (außer Entwürfen), dessen Laufzeit diesen Tag enthält.
- **Betroffene Objekte:** mehrere Mietverhältnisse, Mieteinheit (nur Einheitsnummer)
- **Aggregat:** mehrere Mietverhältnisse → Anwendungsschicht (Domänendienst »Belegung prüfen«), Vergleich über die Operation »überschneidet sich mit« des Wertobjekts Zeitraum
- **Fundstellen:**
  - `src/Service/TenancyService.php::createTenancy()` – Fehlermeldung »Der Vertragszeitraum überschneidet sich …«
  - `src/Repository/TenancyRepository.php::findOverlapping()` – Entwürfe ausgenommen; Ende leer oder Ende ≥ neuer Beginn, und Beginn ≤ neues Ende
  - `src/Service/TenancyService.php::createTenancy()` – zusätzlich: kein laufendes Mietverhältnis der Einheit (`findActiveForUnit()`)
- **Gegenbeobachtung:** (1) Geprüft wird nur beim Anlegen; Kündigung, Aufhebung und Auszug ändern das Mietende ohne erneute Prüfung. (2) Weil Entwürfe nicht zählen, sind zwei Entwürfe für dieselbe Einheit möglich (die Einheit ist nach dem ersten »reserviert«, und reservierte Einheiten sind zugelassen, REGEL-01). (3) Die zweite Prüfung »kein laufendes Mietverhältnis« verbietet einen Nachmieter-Vertrag während der Kündigungsfrist, auch wenn sich die Laufzeiten nicht überschneiden.
- **Status:** widersprüchlich
- **Entscheidung:** Maßgeblich ist allein die Überschneidung der Laufzeiten; die Zusatzprüfung »kein laufendes Mietverhältnis« entfällt. Beim Aktivieren wird die Überschneidung erneut geprüft, damit von zwei Entwürfen nur einer wirksam werden kann.
- **Offene Frage:** F-12 – Nachmieter während der Kündigungsfrist?

---

## REGEL-16

- **Regel:** Das Mietende liegt nie vor dem Mietbeginn.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-05 anlegen, AF-08 kündigen, AF-09 aufheben, AF-10 Auszug protokollieren
- **Vorbedingung:** –
- **Nachbedingung:** –
- **Invariante:** Ist ein Mietende gesetzt, gilt Mietende ≥ Mietbeginn. (Regel des Wertobjekts Zeitraum – jede Änderung der Laufzeit muss sie einhalten.)
- **Betroffene Objekte:** Mietverhältnis (Laufzeit)
- **Aggregat:** Mietverhältnis
- **Fundstellen:**
  - `src/Service/TenancyService.php::createTenancy()` – Fehlermeldung »Vertragsende liegt vor Vertragsbeginn.«
- **Gegenbeobachtung:** Nur beim Anlegen geprüft. Die Aufhebung setzt ein beliebiges Mietende (Standard: heute) – wird ein Entwurf mit Mietbeginn im nächsten Monat heute aufgehoben, liegt das Ende vor dem Beginn (`terminateImmediately()`). Ebenso kann beim Auszug ein Übergabedatum vor dem Mietbeginn das Mietende werden (`recordHandover()`).
- **Status:** widersprüchlich
- **Entscheidung:** Invariante des Wertobjekts Zeitraum; gilt für jeden Befehl, der die Laufzeit ändert.
- **Offene Frage:** –

---

## REGEL-17

- **Regel:** Jedes Mietverhältnis erhält beim Anlegen eine eindeutige Vertragsnummer im Format »MV-« und fünf Ziffern; sie ändert sich nie.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-05 Mietverhältnis anlegen
- **Vorbedingung:** –
- **Nachbedingung:** Vertragsnummer = »MV-« + (1000 + Anzahl vorhandener Mietverhältnisse + 1), fünfstellig mit führenden Nullen.
- **Invariante:** Keine zwei Mietverhältnisse haben dieselbe Vertragsnummer; die Vertragsnummer eines Mietverhältnisses ändert sich nicht.
- **Betroffene Objekte:** alle Mietverhältnisse
- **Aggregat:** keines – Aufgabe der Ablage (Nummernvergabe)
- **Fundstellen:**
  - `src/Service/TenancyService.php::createTenancy()`
  - `src/Repository/TenancyRepository.php::nextContractNumber()`
  - `src/Entity/Tenancy.php` – Vertragsnummer eindeutig in der Datenbank
- **Gegenbeobachtung:** Wie REGEL-07 bei Meldungen: Die Nummer hängt von der Anzahl ab, nicht von einer fortlaufenden Folge. Bei gleichzeitigem Anlegen entsteht dieselbe Nummer (die Datenbank lehnt das zweite ab). Altdaten mit kleineren Nummern (z. B. MV-00987 in den Beispieldaten) passen nicht ins Schema »ab 1001«.
- **Status:** zu klären
- **Entscheidung:** Im Modell nur: »eindeutig, unveränderlich, Format MV- + 5 Ziffern«. Das Verfahren der Vergabe ist Sache der Ablage.
- **Offene Frage:** Muss die Vertragsnummer lückenlos fortlaufend sein (z. B. für die Buchhaltung)?

---

## REGEL-18

- **Regel:** Werden beim Anlegen Kaltmiete, Nebenkostenvorauszahlung oder Kaution nicht angegeben, werden sie vorbelegt: Kaltmiete und Nebenkostenvorauszahlung aus dem Angebot der Mieteinheit, Kaution mit dem Dreifachen der Kaltmiete.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-05 Mietverhältnis anlegen
- **Vorbedingung:** Ist keine Kaltmiete angegeben, hat die Mieteinheit eine Angebotsmiete (sonst kann nicht angelegt werden).
- **Nachbedingung:** Kaltmiete = angegebener Wert, sonst Angebotsmiete. Nebenkostenvorauszahlung = angegebener Wert, sonst Nebenkostenvorauszahlung (Angebot) der Einheit. Kaution = angegebener Wert, sonst 3 × Kaltmiete (auf Cent gerundet).
- **Invariante:** Kaltmiete, Nebenkostenvorauszahlung und Kaution sind ≥ 0. Spätere Änderungen der Angebotsmiete ändern die Kaltmiete des Mietverhältnisses nicht.
- **Betroffene Objekte:** Mietverhältnis; Mieteinheit (Angebotsmiete, nur gelesen)
- **Aggregat:** Mietverhältnis (die Angebotswerte werden beim Befehl »anlegen« als Werte übergeben)
- **Fundstellen:**
  - `src/Service/TenancyService.php::createTenancy()` – Vorbelegung, Kaution `× 3`
  - `src/Controller/TenancyController.php::new()` – füllt das Formular schon mit den Angebotswerten
  - `src/DataFixtures/AppFixtures.php` – Angebotsmiete später erhöht, »der Vertrag behält den alten Wert«
- **Gegenbeobachtung:** Ein Betrag von 0 gilt im Code als »nicht angegeben« und wird überschrieben. Die Angebotsmiete der Einheit ist optional – fehlt sie, scheitert das Speichern, weil die Kaltmiete Pflicht ist. Eine Obergrenze für die Kaution wird nicht geprüft.
- **Status:** zu klären
- **Entscheidung:** Vorbelegung bleibt als Nachbedingung des Befehls »anlegen«; 0 ist ein gültiger Wert (nur fehlende Werte werden vorbelegt).
- **Offene Frage:** F-15 – Darf die Kaution höher sein als drei Kaltmieten (gesetzliche Obergrenze für Wohnraum)? Gilt das auch für Gewerbe?

---

## REGEL-19

- **Regel:** Der Zustand der Mieteinheit folgt dem Mietverhältnis: angelegt → reserviert, aktiviert → vermietet, gekündigt oder aufgehoben → gekündigt, beendet → frei bzw. Renovierung bei Übergabemängeln.
- **Bereich:** Vermietung → Bestand
- **Anwendungsfall:** AF-03, AF-05, AF-07, AF-08, AF-09, AF-10
- **Vorbedingung:** –
- **Nachbedingung:** nach jedem Ereignis des Mietverhältnisses hat die Mieteinheit den zugehörigen Zustand (Tabelle in `verhalten.md`, Abschnitt 4).
- **Invariante:** –
- **Betroffene Objekte:** Mietverhältnis, Mieteinheit
- **Aggregat:** mehrere → die Mieteinheit reagiert auf die Ereignisse des Mietverhältnisses (E-17)
- **Fundstellen:**
  - `src/Service/TenancyService.php::createTenancy()`, `activate()`, `terminateImmediately()`, `recordHandover()`
  - `src/Controller/TenancyController.php::terminate()`
  - `src/Service/RentalService.php::acceptProspect()`
- **Gegenbeobachtung:** Derselbe Zustand wird an sechs Stellen direkt gesetzt, ohne den bisherigen Zustand der Einheit zu prüfen (z. B. eine gesperrte Einheit wird beim Aktivieren »vermietet«). Zusätzlich ist der Zustand im Bestand frei wählbar (`RentalUnitType`), sodass er dem Mietverhältnis widersprechen kann.
- **Status:** widersprüchlich
- **Entscheidung:** Alternative B aus VL 4: getrennte Aggregate, Abstimmung über Ereignisse. Im Bestand sind reserviert/vermietet/gekündigt nicht mehr von Hand wählbar.
- **Offene Frage:** –

---

## REGEL-20

- **Regel:** Nur ein Entwurf mit mindestens einem Mieter kann aktiviert werden; danach ist das Mietverhältnis aktiv, der Einzug ist geplant und die erste Miete wird gefordert.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-07 Mietverhältnis aktivieren
- **Vorbedingung:** Zustand = Entwurf. Mindestens ein Mieter ist eingetragen. Keine Überschneidung mit einem anderen nicht-Entwurf-Mietverhältnis der Einheit (REGEL-15).
- **Nachbedingung:** Zustand = aktiv. Ereignis »Mietverhältnis aktiviert«. Darauf: Mieteinheit = vermietet; Übergabetermin (Einzug) am Mietbeginn um 10:00 Uhr (1 Stunde); Sollstellungen für Kaltmiete und Nebenkostenvorauszahlung für den Monat des Mietbeginns, fällig am 3. des Monats.
- **Invariante:** –
- **Betroffene Objekte:** Mietverhältnis, Mieterliste; über Ereignis: Mieteinheit, Termin, Sollstellung
- **Aggregat:** Mietverhältnis (Folgen über Ereignis)
- **Fundstellen:**
  - `src/Service/TenancyService.php::activate()`
  - `src/Service/AppointmentService.php::createHandover()` – Termin 10:00, 1 Stunde
  - `src/Service/TenancyService.php::createChargesForMonth()` – fällig am 3.
  - `templates/tenancy/show.html.twig` – Button nur bei Entwurf
- **Gegenbeobachtung:** Code und Oberfläche stimmen überein. Aber: Der Zustand der Einheit wird nicht geprüft; die Vermietung erzeugt selbst Sollstellungen (Wissen der Abrechnung).
- **Status:** bestätigt
- **Entscheidung:** Termin und Sollstellungen sind keine Aufgabe des Mietverhältnisses – andere Kontexte reagieren auf »Mietverhältnis aktiviert« (`kontexte.md`).
- **Offene Frage:** –

---

## REGEL-21

- **Regel:** Ein laufendes (aktives oder gekündigtes) Mietverhältnis hat immer mindestens einen Mieter.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-06 Mieter eines Mietverhältnisses ändern
- **Vorbedingung:** (für »Mieter entfernen«) Ist das Mietverhältnis laufend, bleibt nach dem Entfernen mindestens ein Mieter.
- **Nachbedingung:** –
- **Invariante:** Zustand aktiv oder gekündigt ⇒ Anzahl Mieter ≥ 1.
- **Betroffene Objekte:** Mietverhältnis, Mieterliste
- **Aggregat:** Mietverhältnis – nur die Wurzel ändert die Mieterliste (Beispiel VL 4)
- **Fundstellen:**
  - `src/Service/TenancyService.php::removeTenant()` – »Der letzte Mieter eines aktiven Vertrags kann nicht entfernt werden.«
  - `src/Service/TenancyService.php::activate()` – mindestens ein Mieter
- **Gegenbeobachtung:** Geprüft wird nur der Zustand aktiv. Bei einem **gekündigten** Mietverhältnis kann der letzte Mieter entfernt werden – es läuft dann ohne Vertragspartner weiter.
- **Status:** widersprüchlich
- **Entscheidung:** Die Regel gilt für »laufend« (aktiv und gekündigt), nicht nur für aktiv (E-24).
- **Offene Frage:** F-10 (bestätigen lassen)

---

## REGEL-22

- **Regel:** Mieter können nur bei einem Entwurf, einem aktiven oder einem gekündigten Mietverhältnis aufgenommen oder entfernt werden; jeder Mieter steht höchstens einmal in einem Mietverhältnis.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-06 Mieter eines Mietverhältnisses ändern
- **Vorbedingung:** Zustand = Entwurf, aktiv oder gekündigt. Beim Aufnehmen: der Mieter ist noch nicht eingetragen. Beim Entfernen: REGEL-21.
- **Nachbedingung:** Mieterliste enthält den Mieter (aufnehmen) bzw. nicht mehr (entfernen).
- **Invariante:** Ein Mieter steht höchstens einmal in der Mieterliste eines Mietverhältnisses.
- **Betroffene Objekte:** Mietverhältnis, Mieterliste (Mieternummern)
- **Aggregat:** Mietverhältnis
- **Fundstellen:**
  - `src/Service/TenancyService.php::addTenant()`, `removeTenant()`
  - `src/Entity/Tenancy.php::addTenant()` – doppelter Mieter wird stillschweigend ignoriert
  - `templates/tenancy/show.html.twig` – Buttons »entfernen« und »Mieter hinzufügen« in jedem Zustand
- **Gegenbeobachtung:** Das Altsystem erlaubt Änderungen der Mieterliste in jedem Zustand, auch nach Aufhebung oder Ende.
- **Status:** widersprüchlich
- **Entscheidung:** Nach Aufhebung und Ende ist die Mieterliste unveränderlich – sie dokumentiert, wer Vertragspartner war.
- **Offene Frage:** –

---

## REGEL-23

- **Regel:** Nur ein aktives Mietverhältnis kann gekündigt werden; das Mietende liegt dann drei Monate nach dem Kündigungsdatum, und die Rückgabe wird geplant.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-08 Mietverhältnis kündigen
- **Vorbedingung:** Zustand = aktiv. Ein Kündigungsdatum ist angegeben.
- **Nachbedingung:** Zustand = gekündigt. Kündigungsdatum gesetzt. Mietende = Kündigungsdatum + Kündigungsfrist (3 Monate). Ereignis »Mietverhältnis gekündigt«. Darauf: Mieteinheit = gekündigt; Übergabetermin (Auszug) am Mietende um 10:00 Uhr.
- **Invariante:** REGEL-16 (Mietende ≥ Mietbeginn).
- **Betroffene Objekte:** Mietverhältnis; über Ereignis: Mieteinheit, Übergabetermin
- **Aggregat:** Mietverhältnis (Folgen über Ereignis)
- **Fundstellen:**
  - `src/Controller/TenancyController.php::terminate()` – Fachlogik im Controller, `+3 months`
  - `config/services.yaml` – Parameter `app.notice_period_months: 3`, wird nirgends benutzt
  - `templates/tenancy/show.html.twig` – Button nur bei aktiv **und** Merker `noticeGiven` = nein
- **Gegenbeobachtung:** Die Frist von drei Monaten steht fest im Code – nirgends in der Oberfläche oder im Vertrag. Fehlt das Kündigungsdatum, gilt »heute«; ein Datum in der Zukunft oder vor dem Mietbeginn wird nicht abgelehnt. Bei einem befristeten Mietverhältnis überschreibt die Kündigung das Mietende, auch wenn es früher lag (B-08). Der Merker `noticeGiven` wird nicht gesetzt (B-05).
- **Status:** widersprüchlich
- **Entscheidung:** Die Frist gehört ins Modell, nicht in den Controller: benannter Wert **Kündigungsfrist** (heute 3 Monate). Der ungenutzte Parameter zeigt, dass sie einmal einstellbar sein sollte (E-23). Eine Kündigung verlängert ein befristetes Mietverhältnis nicht: Mietende = das frühere von bisherigem Mietende und Kündigungsdatum + Frist (Annahme, F-17). Der Merker entfällt (E-14).
- **Offene Frage:** F-06 – Gilt die Frist immer (lange Mietdauer, Gewerbe, Kündigung durch die Hausverwaltung)? F-17 – befristetes Mietverhältnis kündigen?

---

## REGEL-24

- **Regel:** Nur die Administration kann ein aktives oder gekündigtes Mietverhältnis aufheben; es endet dann ohne Kündigungsfrist zum gewählten Datum, und die Rückgabe wird geplant.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-09 Mietverhältnis aufheben
- **Vorbedingung:** Zustand = aktiv oder gekündigt. Akteur = Administration. Gewähltes Mietende ≥ Mietbeginn (REGEL-16).
- **Nachbedingung:** Zustand = aufgehoben. Mietende = gewähltes Datum (Vorschlag: heute). Ereignis »Mietverhältnis aufgehoben«. Darauf: Mieteinheit = gekündigt; Übergabetermin (Auszug) am Mietende um 10:00 Uhr.
- **Invariante:** REGEL-16.
- **Betroffene Objekte:** Mietverhältnis; über Ereignis: Mieteinheit, Übergabetermin
- **Aggregat:** Mietverhältnis (Folgen über Ereignis)
- **Fundstellen:**
  - `src/Service/TenancyService.php::terminateImmediately()`
  - `src/Controller/TenancyController.php::terminateImmediately()` – Standard »heute«
  - `templates/tenancy/show.html.twig` – Button nur bei aktiv/gekündigt und `ROLE_ADMIN`
- **Gegenbeobachtung:** Weder Zustand noch Rolle werden im Code geprüft – nur das Template blendet den Button aus. Über einen direkten Aufruf kann jeder angemeldete Nutzer auch Entwürfe und beendete Mietverhältnisse aufheben (B-02). Zusätzlich wird der Merker `noticeGiven` gesetzt.
- **Status:** widersprüchlich
- **Entscheidung:** Alternative B aus VL 4: Rolle und Zustand stehen im Modell (Vorbedingung) – die neue Schnittstelle erzwingt sie (E-22). Der Merker entfällt (E-14).
- **Offene Frage:** F-07 – Sind Aufhebung (einvernehmlich) und fristlose Kündigung ein Vorgang? F-13 – Gilt ein aufgehobenes Mietverhältnis bis zum Auszug noch als »laufend«?

---

## REGEL-25

- **Regel:** Ein Einzug kann für ein aktives oder gekündigtes Mietverhältnis genau einmal protokolliert werden; der Zustand ändert sich dadurch nicht.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-10 Übergabe protokollieren (Einzug)
- **Vorbedingung:** Zustand = aktiv oder gekündigt. Es gibt noch keine Übergabe der Art Einzug.
- **Nachbedingung:** Übergabe (Einzug) mit Übergabedatum, Zählerständen, Schlüsselanzahl, Übergabemängeln und Bemerkung ist Teil des Mietverhältnisses. Zustand unverändert. Ereignis »Einzug protokolliert«.
- **Invariante:** Höchstens eine Übergabe je Art (Einzug, Auszug) pro Mietverhältnis.
- **Betroffene Objekte:** Mietverhältnis, Übergabe
- **Aggregat:** Mietverhältnis
- **Fundstellen:**
  - `src/Service/TenancyService.php::recordHandover()`
  - `src/Controller/TenancyController.php::handover()` – Art vorbelegt: Auszug bei gekündigt/aufgehoben, sonst Einzug
  - `templates/tenancy/show.html.twig` – Button bei aktiv, gekündigt, aufgehoben
- **Gegenbeobachtung:** Beliebig viele Einzüge, in jedem Zustand (B-06).
- **Status:** widersprüchlich
- **Entscheidung:** Höchstens ein Einzug je Mietverhältnis (E-15); der Einzug kann nachgeholt werden, solange das Mietverhältnis läuft.
- **Offene Frage:** –

---

## REGEL-26

- **Regel:** Ein Auszug beendet ein gekündigtes oder aufgehobenes Mietverhältnis (bei befristetem Mietverhältnis auch ein aktives nach Ablauf); die Mieteinheit wird danach frei oder bei Übergabemängeln renoviert.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-10 Übergabe protokollieren (Auszug)
- **Vorbedingung:** Zustand = gekündigt oder aufgehoben – **oder** Zustand = aktiv, das Mietverhältnis ist befristet und das Mietende ist erreicht. Es gibt noch keine Übergabe der Art Auszug.
- **Nachbedingung:** Übergabe (Auszug) ist Teil des Mietverhältnisses. Zustand = beendet. Mietende = Übergabedatum, falls bisher keines gesetzt war. Ereignis »Mietverhältnis beendet« (mit »Übergabemängel ja/nein«). Darauf: Mieteinheit = Renovierung, wenn Übergabemängel eingetragen sind, sonst frei.
- **Invariante:** REGEL-16; höchstens ein Auszug (REGEL-25).
- **Betroffene Objekte:** Mietverhältnis, Übergabe; über Ereignis: Mieteinheit
- **Aggregat:** Mietverhältnis (Folgen über Ereignis)
- **Fundstellen:**
  - `src/Service/TenancyService.php::recordHandover()`
  - `templates/tenancy/show.html.twig` – Button bei aktiv, gekündigt, aufgehoben
- **Gegenbeobachtung:** Der Zustand wird nicht geprüft: Auszug beendet auch ein nie gekündigtes aktives (B-01) oder einen Entwurf (B-03).
- **Status:** widersprüchlich
- **Entscheidung:** Die Kante aktiv → beendet bleibt nur für befristete Mietverhältnisse nach Ablauf (E-21). Ein unbefristetes Mietverhältnis muss vorher gekündigt oder aufgehoben werden.
- **Offene Frage:** –

---

## REGEL-27

- **Regel:** Einem Interessenten kann nur einmal zugesagt werden, und nur solange er weder zugesagt noch abgesagt ist; aus ihm wird dann ein Mieter, und seine gewünschte Einheit wird für ihn reserviert, wenn sie frei ist.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-03 Zusage erteilen
- **Vorbedingung:** Zustand des Interessenten = neu, Besichtigung geplant oder Bewerbung eingegangen.
- **Nachbedingung:** Zustand = zugesagt. Ereignis »Interessent zugesagt«. Darauf: ein Mieter mit Name und Kontakt des Interessenten und Herkunft = Interessentennummer ist angelegt; ist die gewünschte Einheit frei, ist sie für diesen Mieter reserviert (sonst bleibt sie unverändert).
- **Invariante:** Aus einem Interessenten entsteht höchstens ein Mieter.
- **Betroffene Objekte:** Interessent; über Ereignis: Mieter, Mieteinheit
- **Aggregat:** Interessent (Folgen über Ereignis)
- **Fundstellen:**
  - `src/Service/RentalService.php::acceptProspect()`
  - `src/Controller/LettingController.php::accept()` – Weiterleitung zur Vertragsanlage
  - `templates/letting/prospects.html.twig` Z. 20 – Button nur bei neu, Besichtigung geplant, Bewerbung eingegangen
- **Gegenbeobachtung:** Der Zustand wird nur im Template geprüft; eine zweite Zusage legt einen zweiten Mieter an (F-03). Der Name wird am letzten Leerzeichen in Vor- und Nachname geteilt. Die Herkunft steht nur als Freitext in der Notiz des Mieters. Ist die Einheit nicht frei, wird das nicht gemeldet.
- **Status:** widersprüchlich
- **Entscheidung:** Zustandsprüfung im Modell; Name wird schon beim Interessenten als Personenname erfasst (kein Teilen mehr); Herkunft als Verweis (E-18). Die Reservierung merkt sich den Mieter (E-20).
- **Offene Frage:** F-03 (bestätigen lassen)

---

## REGEL-28

- **Regel:** Für einen Interessenten, dem weder zugesagt noch abgesagt wurde, kann eine Besichtigung einer Mieteinheit geplant werden; ein neuer Interessent steht danach auf »Besichtigung geplant«.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-02 Besichtigung planen
- **Vorbedingung:** Zustand des Interessenten = neu, Besichtigung geplant oder Bewerbung eingegangen. Mieteinheit und Besichtigungszeitpunkt sind angegeben.
- **Nachbedingung:** Eine Besichtigung (Zustand geplant) ist Teil des Interessenten. War der Interessent neu, ist er jetzt »Besichtigung geplant«; sonst bleibt sein Zustand. Ereignis »Besichtigung geplant«. Darauf: Termin »Besichtigung« zum Besichtigungszeitpunkt, 30 Minuten.
- **Invariante:** –
- **Betroffene Objekte:** Interessent, Besichtigung; über Ereignis: Termin
- **Aggregat:** Interessent
- **Fundstellen:**
  - `src/Service/RentalService.php::planViewing()`
  - `src/Controller/LettingController.php::newViewing()` – Vorschlag morgen 17:00, Mitarbeiter = angemeldeter Benutzer (nur Vorschläge der Oberfläche, keine Regel)
  - `templates/letting/prospects.html.twig` Z. 20 – Button nur bei neu, Besichtigung geplant, Bewerbung eingegangen
- **Gegenbeobachtung:** Der Code setzt den Interessenten immer auf »Besichtigung geplant« – auch aus »Bewerbung eingegangen«, »zugesagt« oder »abgesagt« (B-10).
- **Status:** widersprüchlich
- **Entscheidung:** Eine Besichtigung setzt den Zustand nicht zurück; zugesagte und abgesagte Interessenten können keine Besichtigung mehr bekommen (E-25).
- **Offene Frage:** F-04 – Wie wird eine Besichtigung durchgeführt oder abgesagt?

---

## REGEL-29

- **Regel:** Der Zustand eines Interessenten ändert sich nur in der festgelegten Folge (neu → Besichtigung geplant → Bewerbung eingegangen → zugesagt oder abgesagt); zugesagt und abgesagt sind endgültig.
- **Bereich:** Vermietung
- **Anwendungsfall:** AF-01 Interessent erfassen und pflegen
- **Vorbedingung:** Jeder Zustandswechsel ist ein Übergang aus `verhalten.md`, Abschnitt 2.
- **Nachbedingung:** –
- **Invariante:** Ein zugesagter oder abgesagter Interessent ändert seinen Zustand nicht mehr.
- **Betroffene Objekte:** Interessent
- **Aggregat:** Interessent
- **Fundstellen:**
  - `src/Form/ProspectType.php` – Feld `status` frei wählbar
  - `src/Controller/LettingController.php::editProspect()`, `handleProspectForm()`
- **Gegenbeobachtung:** Der Zustand ist im Formular frei wählbar, z. B. von »zugesagt« zurück auf »neu« (B-09). »Bewerbung eingegangen« und »Absage« haben keine eigene Aktion (B-12).
- **Status:** widersprüchlich
- **Entscheidung:** Der Zustand ist kein Formularfeld mehr; eigene Befehle »Bewerbung erfassen« und »absagen« (E-25). E-04 (»Absage als Teil von AF-01«) wird damit für das Zielsystem überholt.
- **Offene Frage:** F-05 – Was genau ist eine Bewerbung?
