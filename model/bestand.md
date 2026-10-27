# Bestand – Vermietung (Leitfaden Schritt 1)

Bestandsaufnahme aller Aktionen der **Vermietung** im Altsystem ImmoVerwalt.
Jede Route ist eine Aktion, die ein Nutzer auslösen kann. Für jede Route wurde die Controller-Methode
(und, wo vorhanden, die Service-Methode) gelesen.

**Eingabe:** `bin/console debug:router`, `bin/console doctrine:mapping:info`, `src/Controller`, `src/Service`, `templates/`

> Hinweis: Die Routen wurden aus den `@Route`-Annotationen im Code übernommen.
> Abgleich mit `docker compose exec php php bin/console debug:router` steht noch aus (siehe Logbuch).
> Methode `ANY` = in der Annotation ist keine Methode angegeben.

## Routen der Vermietung

| Nr. | Route | Methode, URL | Controller → Service | Was passiert fachlich? | Beobachtung |
|---|---|---|---|---|---|
| 1 | `prospect_index` | ANY `/vermietung/interessenten` | `LettingController::prospects()` | Liste aller Mietinteressenten, neueste Anfrage zuerst. Buttons »Besichtigung« und »Zusage« nur bei Status Neu, Besichtigung geplant, Bewerbung eingegangen. | Listenansicht, keine Fachlogik |
| 2 | `prospect_new` | GET, POST `/vermietung/interessenten/neu` | `LettingController::newProspect()` → `handleProspectForm()` | Neue Mietanfrage erfassen: Name, E-Mail, Telefon, gewünschte Einheit, Anfragedatum, Status, Bemerkung. | Status ist im Formular frei wählbar; Speichern direkt im Controller |
| 3 | `prospect_edit` | GET, POST `/vermietung/interessenten/{id}/bearbeiten` | `LettingController::editProspect()` → `handleProspectForm()` | Interessent ändern. **Absage** und **Bewerbung eingegangen** werden nur hier von Hand als Status gesetzt. | keine Prüfung der Statusfolge (z. B. von »Zusage« zurück auf »Neu« möglich) |
| 4 | `prospect_accept` | POST `/vermietung/interessenten/{id}/zusage` | `LettingController::accept()` → `RentalService::acceptProspect()` | **Zusage:** aus dem Interessenten wird ein Mieter angelegt (Name wird am letzten Leerzeichen in Vor- und Nachname geteilt), Interessent → Zusage, gewünschte Einheit → reserviert (nur wenn frei). Danach Weiterleitung zur Vertragsanlage (mit Einheit) bzw. zum Mieter (ohne Einheit). | Statusprüfung nur im Template, nicht im Code → zweite Zusage legt einen zweiten Mieter an |
| 5 | `viewing_index` | ANY `/vermietung/besichtigungen` | `LettingController::viewings()` | Liste aller Besichtigungen. | Status (geplant / durchgeführt / abgesagt) wird angezeigt, kann aber über keine Route geändert werden |
| 6 | `viewing_new` | GET, POST `/vermietung/besichtigungen/neu` | `LettingController::newViewing()` → `RentalService::planViewing()` | **Besichtigung planen:** Einheit, Interessent, Termin (Vorschlag: morgen 17:00), zuständiger Mitarbeiter. Interessent → Besichtigung geplant. Zusätzlich ein Termin vom Typ »Besichtigung« (30 Minuten) im Terminkalender. | setzt den Interessenten auch dann auf »Besichtigung geplant« zurück, wenn er schon »Bewerbung eingegangen« oder »Zusage« hatte |
| 7 | `tenant_index` | ANY `/stammdaten/mieter/` | `TenantController::index()` | Liste und Namenssuche der Mieter mit ihren Verträgen. | im Menü unter »Stammdaten«, fachlich Vermietung (VL 3, Folie 13) |
| 8 | `tenant_new` | GET, POST `/stammdaten/mieter/neu` | `TenantController::new()` → `handleForm()` | Mieter direkt anlegen (ohne vorherigen Interessenten): Anrede, Vor-/Nachname, Kontakt, Geburtsdatum, Voranschrift, Notizen. | zweiter Weg zum Mieter neben der Zusage |
| 9 | `tenant_show` | ANY `/stammdaten/mieter/{id}` | `TenantController::show()` | Mieterdaten und alle seine Mietverhältnisse anzeigen. | Anzeige |
| 10 | `tenant_edit` | GET, POST `/stammdaten/mieter/{id}/bearbeiten` | `TenantController::edit()` → `handleForm()` | Mieterdaten ändern. | – |
| 11 | `tenancy_index` | ANY `/vermietung/mietverhaeltnisse` | `TenancyController::index()` | Liste der Mietverhältnisse mit Filter: **laufende** (aktiv + gekündigt, Standard), Entwürfe, gekündigte, beendete, alle. | Filter »aufgehoben« fehlt in der Oberfläche |
| 12 | `tenancy_new` | GET, POST `/vermietung/mietverhaeltnisse/neu/{unit}` | `TenancyController::new()` → `TenancyService::createTenancy()` | **Mietvertrag als Entwurf anlegen.** Prüfungen: Einheit frei oder reserviert (Controller); kein laufendes Mietverhältnis für die Einheit; kein überschneidender Zeitraum; Ende nicht vor Beginn (Service). Vertragsnummer `MV-0xxxx` wird vergeben. Kaltmiete und NK-Vorauszahlung aus der Einheit vorbelegt, Kaution = 3 × Kaltmiete, falls leer. Beginn-Vorschlag: 1. des Folgemonats. Einheit → reserviert. | Widerspruch REGEL-01: Template bietet den Button nur bei »frei«, Controller lässt auch »reserviert« zu (nötig für den Weg über die Zusage, Nr. 4) |
| 13 | `tenancy_show` | ANY `/vermietung/mietverhaeltnisse/{id}` | `TenancyController::show()` | Vertragsdaten, Mieter, Übergaben, Mieterkonto (aus der Abrechnung) und Termine; Buttons je nach Status. | Seite mischt Vermietung und Abrechnung |
| 14 | `tenancy_activate` | POST `/vermietung/mietverhaeltnisse/{id}/aktivieren` | `TenancyController::activate()` → `TenancyService::activate()` | **»Vertrag unterschrieben – aktivieren«:** nur aus Entwurf, mindestens ein Mieter. Mietverhältnis → aktiv, Einheit → vermietet. Übergabetermin (Einzug) am Mietbeginn 10:00. Erste Sollstellungen (Kaltmiete + NK-Vorauszahlung) für den Monat des Mietbeginns, fällig am 3. | wirkt direkt in Termine und Abrechnung hinein |
| 15 | `tenancy_tenant` | POST `/vermietung/mietverhaeltnisse/{id}/mieter` | `TenancyController::tenants()` → `TenancyService::addTenant()` / `removeTenant()` | Mieter zu einem Mietverhältnis hinzufügen oder entfernen. Der letzte Mieter eines **aktiven** Mietverhältnisses kann nicht entfernt werden. | bei »gekündigt« darf der letzte Mieter entfernt werden; Änderung auch nach Vertragsende möglich |
| 16 | `tenancy_terminate` | POST `/tenancies/{id}/terminate` | `TenancyController::terminate()` (kein Service) | **Kündigung erfassen:** nur bei aktiv. Kündigungsdatum aus dem Formular, Ende = Kündigungsdatum + 3 Monate. Mietverhältnis → gekündigt, Einheit → gekündigt. Rückgabetermin (Auszug) am Vertragsende. | Fachlogik im Controller; englische URL (Altlast); Merker `noticeGiven` wird **nicht** gesetzt; feste Frist 3 Monate (unabhängig von Mietdauer, Wohnung/Gewerbe); leitet zur Einheit statt zum Vertrag |
| 17 | `tenancy_terminate_immediately` | POST `/vermietung/mietverhaeltnisse/{id}/aufheben` | `TenancyController::terminateImmediately()` → `TenancyService::terminateImmediately()` | **Aufhebung / fristlose Kündigung:** Ende = gewähltes Datum (Standard heute), Mietverhältnis → aufgehoben, Merker `noticeGiven` = ja, Einheit → gekündigt, Rückgabetermin. | keine Statusprüfung (auch für Entwurf oder beendetes Mietverhältnis möglich); Button nur für `admin`, die Route selbst für alle angemeldeten Nutzer |
| 18 | `handover_new` | GET, POST `/vermietung/mietverhaeltnisse/{id}/uebergabe` | `TenancyController::handover()` → `TenancyService::recordHandover()` | **Übergabe protokollieren:** Art (Einzug / Auszug), Datum, Zählerstände (Freitext), Anzahl Schlüssel, Mängel, Bemerkungen. Art vorbelegt: Auszug bei gekündigt/aufgehoben, sonst Einzug. Bei **Auszug**: Mietverhältnis → beendet, Ende = Rückgabedatum (falls leer); Einheit → Renovierung, wenn Mängel eingetragen sind, sonst frei. | keine Statusprüfung (Auszug auch bei Entwurf möglich); Einzug ändert keinen Status; Button nur bei aktiv/gekündigt/aufgehoben |

**Ergebnis:** 18 Routen (14 unter `/vermietung` bzw. `tenancy_*`, 4 Mieter-Routen unter `/stammdaten/mieter`).

## Angrenzende Routen (anderer Bereich, aber Teil der Abläufe)

| Route | Bereich | Bezug zur Vermietung |
|---|---|---|
| `unit_show` | Bestand | Einstieg »Mietvertrag anlegen« (nur bei Einheit frei); zeigt Mietverhältnisse und Interessenten der Einheit |
| `account_show` | Abrechnung | Button »Mieterkonto« auf dem Mietverhältnis; das Mieterkonto ist das Mietverhältnis aus Sicht der Abrechnung |
| `charge_generate` | Abrechnung | erzeugt Sollstellungen für alle laufenden Mietverhältnisse |
| `appointment_index` | Termine | zeigt die von der Vermietung angelegten Besichtigungs- und Übergabetermine |

## Entities der Vermietung (`doctrine:mapping:info`)

| Entity | Tabelle | Inhalt | Bemerkung |
|---|---|---|---|
| `Prospect` | `prospect` | Mietinteressent | Status als Zeichenkette (`NEW`, `VIEWING_PLANNED`, `APPLIED`, `ACCEPTED`, `REJECTED`) |
| `Viewing` | `viewing` | Besichtigung | Status `PLANNED`, `DONE`, `CANCELLED` – nur Vorgabewert, wird nie geändert |
| `Tenant` | `tenant` | Mieter | keine Verbindung zum Interessenten außer Freitext in `notes` |
| `Tenancy` | `tenancy`, `tenancy_tenant` | Mietverhältnis / Mietvertrag | Status `DRAFT`, `ACTIVE`, `NOTICE_GIVEN`, `TERMINATED`, `ENDED`; zusätzlich Merker `noticeGiven` (Ticket #213) |
| `Handover` | `handover` | Übergabe / Rückgabe | Art `MOVE_IN`, `MOVE_OUT` |

Mitbenutzt aus anderen Bereichen: `RentalUnit` (Bestand), `Appointment` (Termine), `Charge` (Abrechnung), `User` (Mitarbeiter).

## Rechte

`config/packages/security.yaml`: Alle Routen der Vermietung verlangen nur `ROLE_USER`.
Die Rollen `ROLE_LETTING_MANAGER` (Benutzer `vermieter`) und `ROLE_PROPERTY_MANAGER` (`verwalter`) werden im Code
nirgends geprüft. Einzige Einschränkung: der Button »Aufhebung / fristlos« erscheint nur für `ROLE_ADMIN` (Template).

**Prüffrage:** Ist jede Route der Vermietung erfasst? – Ja, alle Routen aus `LettingController`, `TenancyController`
und `TenantController` (18). Abgleich mit `debug:router` siehe Logbuch.
