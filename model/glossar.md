# Glossar – Vermietung (Leitfaden Schritt 3)

Gemeinsame Fachsprache (Ubiquitous Language) für die Anwendungsfälle AF-01 bis AF-10.
Für jeden Begriff: Wie heißt er in Oberfläche, Code und Datenbank? Was bedeutet er? Welcher Begriff gilt ab jetzt?

**Regel:** Ab jetzt wird in allen Dateien unter `model/` nur der Begriff in der ersten Spalte verwendet.

## Personen und Rollen

| Begriff (gewählt) | Oberfläche | Code | Datenbank | Definition | Begründung der Wahl |
|---|---|---|---|---|---|
| Interessent | »Interessenten«, »Mietinteressenten«, »Neuer Interessent« | `Prospect` | `prospect` | Person, die eine Mieteinheit mieten möchte und noch keinen Vertrag hat. | Kurzform der Oberfläche; »Mietinteressent« nur in einer Überschrift |
| Mieter | »Mieter«, »Neuer Mieter« | `Tenant` | `tenant` | Person, die Vertragspartner in mindestens einem Mietverhältnis ist oder werden soll. | fachlich üblich und überall gleich benannt; gilt in diesem Sinn nur im Kontext Vermietung (siehe `kontexte.md`) |
| Sachbearbeitung Vermietung | Benutzer »vermieter«, Rolle »Vermietung« | `ROLE_LETTING_MANAGER` | `app_user.roles` | Mitarbeiterin der Hausverwaltung, die Interessenten, Mietverhältnisse und Übergaben betreut. | »Vermieter« ist fachlich der Eigentümer als Vertragspartei – das Wort wäre missverständlich |
| Mitarbeiter | »Mitarbeiter« (Besichtigung) | `Viewing::employee` → `User` | `viewing.employee_id` | Angestellte Person der Hausverwaltung, die für eine Besichtigung zuständig ist. | Begriff der Oberfläche; »User« ist Technik |

## Vom Interessenten zum Vertrag

| Begriff (gewählt) | Oberfläche | Code | Datenbank | Definition | Begründung der Wahl |
|---|---|---|---|---|---|
| Anfrage | »Anfrage«, »Anfragedatum« | `Prospect::requestDate` | `prospect.request_date` | Erste Kontaktaufnahme eines Interessenten; ihr Datum ordnet die Interessenten. | Oberfläche; kein eigenes Objekt, nur Datum am Interessenten |
| gewünschte Einheit | »gewünschte Einheit« | `Prospect::unit` | `prospect.unit_id` | Mieteinheit, für die sich der Interessent interessiert (optional). | wörtlich aus der Oberfläche |
| Besichtigung | »Besichtigungen«, »Besichtigung planen«; Termintitel »Besichtigung WE 03 …« | `Viewing`; `Appointment` Typ `BESICHTIGUNG` | `viewing`; `appointment` | Vereinbarter Termin, an dem ein Interessent eine Mieteinheit mit einem Mitarbeiter ansieht. | eindeutig; der Kalendereintrag dazu heißt **Termin** (anderer Kontext) |
| Bewerbung | »Bewerbung eingegangen« | `APPLIED` | `prospect.status` | Der Interessent hat erklärt, die Einheit mieten zu wollen, und seine Unterlagen eingereicht. | nur als Zustand vorhanden; Inhalt (Selbstauskunft?) ist **Annahme**, F-05 |
| Zusage | »Zusage«, »Zusage erteilen« | `prospect_accept`, `RentalService::acceptProspect()`, `ACCEPTED` | `prospect.status = 'ACCEPTED'` | Entscheidung der Hausverwaltung, einem Interessenten die Einheit zu vermieten; danach wird er Mieter. | Oberfläche und Route stimmen überein |
| Absage | »Absage« | `REJECTED` | `prospect.status = 'REJECTED'` | Entscheidung, einem Interessenten die Einheit nicht zu vermieten. | Gegenstück zur Zusage |

## Mietverhältnis

| Begriff (gewählt) | Oberfläche | Code | Datenbank | Definition | Begründung der Wahl |
|---|---|---|---|---|---|
| Mietverhältnis | »Mietverhältnisse«, »Mietvertrag MV-01001«, »Mietvertrag anlegen«, »Mieterkonto MV-01001«, »Vertrag« | `Tenancy` | `tenancy` | Vertragliche Beziehung zwischen einem oder mehreren Mietern und einer Mieteinheit über einen Zeitraum. | fachlich üblicher Begriff; »Mietvertrag« ist nur das Dokument, »Mieterkonto« ist die Sicht der Abrechnung (VL 3) |
| Vertragsnummer | »Vertrag«, »MV-01001« | `Tenancy::contractNumber` | `tenancy.contract_number` | Eindeutige fachliche Nummer eines Mietverhältnisses im Format `MV-` + fünf Ziffern. | Fachleute nennen das Mietverhältnis über diese Nummer |
| Mietbeginn | »Vertragsbeginn«, »Beginn« | `startDate` | `tenancy.start_date` | Tag, ab dem das Mietverhältnis gilt und Miete geschuldet ist. | »Miet-« statt »Vertrags-«, passend zu »Mietverhältnis« |
| Mietende | »Vertragsende«, »Ende«; leer = »unbefristet« | `endDate` | `tenancy.end_date` | Letzter Tag des Mietverhältnisses; leer bei unbefristetem Mietverhältnis. Entsteht durch Befristung, Kündigung, Aufhebung oder Rückgabe. | wie Mietbeginn |
| befristet / unbefristet | »Vertragsende (bei Befristung)«, »unbefristet« | `endDate` gesetzt / `null` | `end_date` | Ein Mietverhältnis ist befristet, wenn das Mietende schon bei Anlage feststeht. | Oberfläche |
| Mieteinheit | »Mieteinheiten«, »Einheit«, »Einheit WE 03 links« | `RentalUnit` | `rental_unit` | Wohnung oder Gewerbeeinheit in einem Gebäude, die vermietet werden kann. | wie im Fachmodell; aus dem Kontext Bestand übernommen |

## Miete und Kaution

| Begriff (gewählt) | Oberfläche | Code | Datenbank | Definition | Begründung der Wahl |
|---|---|---|---|---|---|
| Kaltmiete | »Kaltmiete« | `Tenancy::coldRent` | `tenancy.cold_rent` | Im Mietverhältnis vereinbarte monatliche Miete ohne Nebenkosten. | fachlich üblich |
| Angebotsmiete | »Kaltmiete (Angebot)« | `RentalUnit::coldRent` | `rental_unit.cold_rent` | Kaltmiete, zu der eine Mieteinheit angeboten wird; Vorschlag beim Anlegen eines Mietverhältnisses. | **neuer Begriff**: im Code heißen beide `coldRent`, fachlich sind es zwei Dinge (Angebot ≠ Vereinbarung) |
| Nebenkostenvorauszahlung | »NK-Vorauszahlung«, »NK-VZ« | `serviceChargeAdvance`; Sollposition `SERVICE_CHARGE` | `service_charge_advance` | Monatlicher Abschlag auf die Betriebskosten, der später abgerechnet wird. | ausgeschrieben statt Abkürzung |
| Gesamtmiete | »Gesamtmiete«, »Monatsmiete« | `Tenancy::getTotalRent()` | – (berechnet) | Kaltmiete + Nebenkostenvorauszahlung. | Oberfläche des Mietverhältnisses; »Monatsmiete« nur im Mieterkonto |
| Kaution | »Kaution« | `deposit` | `tenancy.deposit` | Sicherheitsleistung des Mieters; Vorschlag im Altsystem: 3 × Kaltmiete. | überall gleich |

## Kündigung und Ende

| Begriff (gewählt) | Oberfläche | Code | Datenbank | Definition | Begründung der Wahl |
|---|---|---|---|---|---|
| Kündigung | »Kündigung erfassen«, »Kündigung vom« | `tenancy_terminate`, `terminate()`, `noticeDate`, `NOTICE_GIVEN`, `noticeGiven` | `notice_date`, `notice_given` | Einseitige Erklärung, das Mietverhältnis unter Einhaltung der Kündigungsfrist zu beenden. | Oberfläche; `terminate` wird im Code auch für die Aufhebung benutzt und ist deshalb ungeeignet |
| Kündigungsdatum | »Kündigung vom« | `noticeDate` | `tenancy.notice_date` | Tag, an dem die Kündigung erklärt wurde; Startpunkt der Kündigungsfrist. | eindeutig |
| Kündigungsfrist | – (nicht sichtbar) | `modify('+3 months')` in `TenancyController::terminate()` | – | Zeitraum zwischen Kündigungsdatum und Mietende; im Altsystem fest 3 Monate. | fachlicher Begriff; im Altsystem nur als Zahl im Code, F-06 |
| Aufhebung | »Aufhebung / fristlos«, »aufheben«, »aufgehoben« | `tenancy_terminate_immediately`, `terminateImmediately()`, `TERMINATED` | `tenancy.status = 'TERMINATED'` | Sofortige Beendigung eines Mietverhältnisses zu einem gewählten Datum, ohne Kündigungsfrist. | Oberfläche; **Achtung:** das Altsystem fasst einvernehmliche Aufhebung und fristlose Kündigung zusammen – fachlich verschieden, F-07 |

## Übergabe

| Begriff (gewählt) | Oberfläche | Code | Datenbank | Definition | Begründung der Wahl |
|---|---|---|---|---|---|
| Übergabe | »Übergaben«, »Übergabe / Rückgabe«, »Übergabe dokumentiert« | `Handover` | `handover` | Protokollierter Termin, an dem die Mieteinheit an den Mieter übergeben (Einzug) oder vom Mieter zurückgegeben (Auszug) wird. | Oberbegriff in Code und Oberfläche (Tabelle »Übergaben«) |
| Einzug | »Übergabe (Einzug)«, »Wohnungsübergabe (Einzug)« | `MOVE_IN` | `handover.type = 'MOVE_IN'` | Art der Übergabe zu Beginn des Mietverhältnisses. | kurz und eindeutig |
| Auszug | »Rückgabe (Auszug)«, »Wohnungsrückgabe (Auszug)« | `MOVE_OUT` | `handover.type = 'MOVE_OUT'` | Art der Übergabe am Ende des Mietverhältnisses; beendet das Mietverhältnis. | »Rückgabe« bleibt als Synonym in Gesprächen erlaubt, im Modell heißt es »Übergabe (Auszug)« |
| Zählerstand | »Zählerstände« | `meterReadings` (Freitext) | `handover.meter_readings` | Bei der Übergabe abgelesener Stand eines Zählers (Strom, Wasser, Gas …). | Oberfläche; im Altsystem nur Freitext |
| Schlüsselanzahl | »Anzahl Schlüssel«, »Schlüssel« | `keyCount` | `handover.key_count` | Anzahl der übergebenen bzw. zurückgegebenen Schlüssel. | Oberfläche |
| Übergabemangel | »festgestellte Mängel«, »Mängel« | `Handover::defects` | `handover.defects` | Bei einer Übergabe festgestellter Schaden an der Mieteinheit; bei Auszug Grund für die Renovierung. | **neuer Begriff**: »Mangel« wird im Kontext Meldungen für gemeldete Schäden benutzt – Zusatz verhindert Verwechslung |

## Zustände

| Begriff (gewählt) | Oberfläche | Code | Datenbank | Definition | Begründung der Wahl |
|---|---|---|---|---|---|
| Zustand Interessent: neu, Besichtigung geplant, Bewerbung eingegangen, zugesagt, abgesagt | »Neu«, »Besichtigung geplant«, »Bewerbung eingegangen«, »Zusage«, »Absage« | `NEW`, `VIEWING_PLANNED`, `APPLIED`, `ACCEPTED`, `REJECTED` | `prospect.status` | Stand der Anfrage eines Interessenten. | Partizip für den Zustand (»zugesagt«), Hauptwort für die Handlung (»Zusage«) |
| Zustand Besichtigung: geplant, durchgeführt, abgesagt | Rohwerte `PLANNED` usw. werden angezeigt | `PLANNED`, `DONE`, `CANCELLED` | `viewing.status` | Stand einer Besichtigung. | deutsche Begriffe gewählt; Oberfläche zeigt nur Code-Werte |
| Zustand Mietverhältnis: Entwurf, aktiv, gekündigt, aufgehoben, beendet | »Entwurf«, »aktiv«, »gekündigt«, »aufgehoben«, »beendet« | `DRAFT`, `ACTIVE`, `NOTICE_GIVEN`, `TERMINATED`, `ENDED` | `tenancy.status` | Stand eines Mietverhältnisses (Details: `verhalten.md`, Übung 4). | Oberfläche (`_macros.html.twig`) |
| laufend | »laufende« (Filter) | `findCurrent()`, `getCurrentTenancy()`; Kommentar in `findActiveForUnit()`: »zählen ebenfalls als aktiv« | – | Ein Mietverhältnis ist laufend, wenn es aktiv oder gekündigt ist – es gilt noch und die Einheit ist belegt. | »aktiv« ist schon ein Zustand; der Code benutzt »aktiv« in beiden Bedeutungen – »laufend« trennt sie |
| Zustand Mieteinheit: frei, reserviert, vermietet, gekündigt, Renovierung, gesperrt | »FREI«, »RESERVIERT«, »VERMIETET«, »GEKÜNDIGT«, »RENOVIERUNG«, »GESPERRT« | `AVAILABLE`, `RESERVED`, `RENTED`, `NOTICE_GIVEN`, `RENOVATION`, `BLOCKED` | `rental_unit.status` | Stand einer Mieteinheit aus Sicht der Vermietbarkeit. | »frei« statt »verfügbar«: so in Oberfläche, Fehlermeldung (»nicht frei«) und Leerstand; `fachmodell.md` und REGEL-01 sind anzupassen (Logbuch) |

## Verworfene Begriffe

| Begriff | Warum verworfen | stattdessen |
|---|---|---|
| Mietvertrag | bezeichnet das unterschriebene Dokument, nicht die Beziehung über die Zeit | Mietverhältnis |
| Mieterkonto (für das Mietverhältnis) | ist die Sicht der Abrechnung auf das Mietverhältnis | Mietverhältnis (Vermietung) / Mieterkonto (Abrechnung) |
| Vermieter (für den Mitarbeiter) | fachlich der Eigentümer als Vertragspartei | Sachbearbeitung Vermietung |
| verfügbar | Oberfläche und Fehlermeldungen sagen »frei« | frei |
| Rückgabe (als eigenes Objekt) | ist dieselbe Sache wie die Übergabe, nur die Art ist anders | Übergabe (Auszug) |
| Prospect, Tenancy, Handover, … | englische Code-Namen, kein Begriff der Fachleute | deutsche Begriffe oben |

**Prüffrage:** Wird jeder Begriff in allen Dateien unter `model/` gleich verwendet? – In `bestand.md`, `anwendungsfaelle.md`,
`kontexte.md` ja. In `fachmodell.md` und `regelkatalog.md` steht noch »verfügbar« → anpassen (Logbuch, offene Aufgabe).
