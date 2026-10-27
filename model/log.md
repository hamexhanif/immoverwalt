# Logbuch

Entscheidungen, Begründungen, verworfene Wege und KI-Einsatz. Bewertet wird auch der Weg.

---

## 2026-10-26 – Übung 3: Fachsprache und Kontexte (Leitfaden Schritte 1–4, Vermietung)

### Vorgehen

1. Routen der Vermietung aus `LettingController`, `TenancyController` und `TenantController` gesammelt und jede
   Controller- und Service-Methode gelesen → `bestand.md` (18 Routen).
2. Routen zu Anwendungsfällen gebündelt → `anwendungsfaelle.md` (AF-01 bis AF-10).
3. Begriffe in Oberfläche (Templates, Formulare), Code (Entities) und Datenbank (Migration
   `Version20190301120000.php`) verglichen → `glossar.md`.
4. Anwendungsfälle und Begriffe den Kontexten zugeordnet → `kontexte.md`.

### Entscheidungen

| Nr. | Entscheidung | Begründung | verworfen |
|---|---|---|---|
| E-01 | Die vier Mieter-Routen (`tenant_*`) gehören zum Bestand der **Vermietung**, obwohl sie unter `/stammdaten` liegen. | Der Mieter ist Vertragspartner des Mietverhältnisses; Menü und URL sind keine Kontextgrenze (VL 3, Folie 13). | Mieter im Bestand führen (wie im Menü) |
| E-02 | Listen- und Detailansichten (`*_index`, `*_show`) sind keine eigenen Anwendungsfälle, sondern werden den Anwendungsfällen zugeordnet. | Leitfaden Schritt 2: Listenansichten ohne Fachlogik weglassen oder zusammenfassen. So bleibt jede Route nachvollziehbar zugeordnet. | eigener AF »Mietverhältnisse einsehen« – hat kein fachliches Ziel |
| E-03 | Nummerierung AF-01 bis AF-10 für die Vermietung; Bestand und Meldungen/Aufgaben folgen ab AF-11. | Wir haben mit der Vermietung angefangen. Die Nummern im Leitfaden (AF-07 = kündigen) sind nur ein Beispiel. | Nummern aus dem Leitfaden übernehmen – dann fehlen Nummern für Interessent und Besichtigung |
| E-04 | »Interessent absagen« ist Teil von AF-01, kein eigener Anwendungsfall. | Im Altsystem gibt es dafür keine Aktion, nur einen Statuswert im Formular. Für das Zielsystem neu prüfen (Zusage hat eine eigene Aktion, Absage nicht – unsymmetrisch). | eigener AF ohne Route |
| E-05 | **Mietverhältnis** statt »Mietvertrag« bzw. »Mieterkonto«. | Vertrag = Dokument, Mieterkonto = Sicht der Abrechnung (VL 3). | Mietvertrag (so heißt es auf der Detailseite) |
| E-06 | Neuer Begriff **Angebotsmiete** für `RentalUnit::coldRent`. | Im Code heißen Angebot und vereinbarte Miete gleich (`coldRent`); die Oberfläche unterscheidet schon (»Kaltmiete (Angebot)«). Ohne Trennung wäre unklar, welche Miete eine Regel meint. | »Kaltmiete« für beides |
| E-07 | Neuer Sammelbegriff **laufend** = aktiv oder gekündigt. | `TenancyRepository::findActiveForUnit()` zählt gekündigte Verträge »ebenfalls als aktiv« – »aktiv« hätte sonst zwei Bedeutungen. Die Oberfläche nennt den Filter schon »laufende«. | »aktiv« im weiten Sinn |
| E-08 | Zustand der Mieteinheit **frei** statt »verfügbar«. | Oberfläche (»FREI«), Fehlermeldung (»Die Einheit ist nicht frei«) und Leerstandsbericht. Folge: `fachmodell.md` und REGEL-01 anpassen. | »verfügbar« aus der Vorlage des Fachmodells |
| E-09 | **Übergabe** als Oberbegriff mit Art **Einzug** / **Auszug**; »Rückgabe« nur als Synonym. | Code (`Handover`) und Oberfläche (Tabelle »Übergaben«) nutzen schon einen Oberbegriff; beides ist dasselbe Protokoll. | zwei Begriffe Übergabe und Rückgabe als getrennte Objekte |
| E-10 | **Übergabemangel** statt »Mangel«. | »Mangel« ist im Kontext Meldungen/Aufgaben der gemeldete Schaden – an der Grenze ändert sich die Bedeutung. | »Mangel« für beides |
| E-11 | Akteur **Sachbearbeitung Vermietung** statt »Vermieter«. | »Vermieter« ist fachlich der Eigentümer; der Benutzer `vermieter` ist eine Mitarbeiterin. | Rollenname aus dem Altsystem übernehmen |
| E-12 | Termin gehört zu Meldungen/Aufgaben; die Vermietung legt Termine nur an. | Vorschlag VL 3, Folie 14; der Termin hat einen eigenen Kalender (`/vorgaenge/termine`). | eigener Kontext »Termine« (Regelkatalog nennt »Termine« als Bereich) – vorerst nicht, weil kaum eigene Regeln |

### Offene Fragen an den Fachbereich

| Nr. | Frage | betrifft |
|---|---|---|
| F-01 | Wer darf was? Das Altsystem prüft keine Rollen. Darf z. B. die Objektverwaltung Mietverhältnisse kündigen? | alle AF |
| F-02 | Darf für eine **reservierte** Einheit ein Mietverhältnis angelegt werden? (Template: nein, Code: ja, Weg über die Zusage braucht es.) | AF-03, AF-05, REGEL-01 |
| F-03 | Darf ein Interessent mehrfach eine Zusage bekommen? Heute entsteht dann ein zweiter Mieter. | AF-03 |
| F-04 | Wie wird eine Besichtigung als durchgeführt oder abgesagt markiert? Die Zustände gibt es, eine Aktion nicht. | AF-02 |
| F-05 | Was genau ist eine »Bewerbung« – Selbstauskunft, Unterlagen, mündliche Zusage? | AF-01 |
| F-06 | Gilt die Kündigungsfrist immer 3 Monate – auch bei langer Mietdauer, Gewerbe oder Kündigung durch die Hausverwaltung? | AF-08 |
| F-07 | Sind Aufhebung (einvernehmlich) und fristlose Kündigung fachlich ein Vorgang? Das Altsystem trennt sie nicht. | AF-09 |
| F-08 | Darf ein Entwurf oder ein beendetes Mietverhältnis aufgehoben werden? Das Altsystem prüft das nicht. | AF-09 |
| F-09 | Wofür steht der Merker »Kündigung erfolgt« (`noticeGiven`, Ticket #213)? Er wird nur bei der Aufhebung gesetzt, nicht bei der Kündigung. | AF-08, AF-09 |
| F-10 | Darf der letzte Mieter eines **gekündigten** Mietverhältnisses entfernt werden? | AF-06 |

### Hinweise für den Regelkatalog (Übung 4)

Beim Lesen gefundene Regelkandidaten, noch nicht in `regelkatalog.md` eingetragen:

- Kein überschneidender Zeitraum zweier Mietverhältnisse derselben Einheit (`TenancyService::createTenancy()`, `findOverlapping()`).
- Mietende nicht vor Mietbeginn (`createTenancy()`).
- Kaution = 3 × Kaltmiete, falls nicht angegeben (`createTenancy()`).
- Aktivieren nur aus Entwurf und mit mindestens einem Mieter (`TenancyService::activate()`).
- Kündigen nur, wenn aktiv; Mietende = Kündigungsdatum + 3 Monate (`TenancyController::terminate()`).
- Letzter Mieter eines aktiven Mietverhältnisses kann nicht entfernt werden (`TenancyService::removeTenant()`).
- Auszug beendet das Mietverhältnis; Einheit wird frei oder bei Übergabemängeln Renovierung (`TenancyService::recordHandover()`).
- Zusage reserviert die gewünschte Einheit nur, wenn sie frei ist (`RentalService::acceptProspect()`).

### Offene Aufgaben

- [ ] Routen mit `docker compose exec php php bin/console debug:router` abgleichen (bisher aus den Annotationen gelesen).
- [ ] Entities mit `doctrine:mapping:info` abgleichen.
- [x] `fachmodell.md` und REGEL-01: »verfügbar« → »frei« (E-08) – erledigt in Übung 4.
- [ ] Alte Entwürfe `bestand.md` und `anwendungsfaelle.md` im Projektwurzelordner entfernen – gültig sind die Dateien in `model/`.
- [ ] Zu Hause: Schritte 1–4 für **Bestand** und **Meldungen/Aufgaben**.
- [ ] Ausgewählte Abläufe in der laufenden Anwendung nachklicken (z. B. zweite Zusage, Aufhebung eines Entwurfs).

### KI-Einsatz

- Werkzeug: Claude (Anthropic).
- Wofür: Erster Entwurf aller fünf Dateien aus dem Code (Controller, Services, Entities, Formulare, Templates,
  Migration) und dem Leitfaden; Suche nach Widersprüchen zwischen Template, Controller und Service.
- Was wir selbst geprüft haben: *(von der Gruppe ausfüllen – z. B. welche Fundstellen nachgelesen, welche Begriffe geändert)*
- Grenzen: Die KI hat den Code gelesen, aber die Anwendung nicht ausgeführt; `debug:router` steht noch aus.
  Akteure und »Bewerbung« beruhen auf Annahmen.

---

## 2026-10-27 – Übung 4: Bausteine, Zustände, Regeln, Aggregate (Leitfaden Schritte 5–8, Vermietung)

### Vorgehen

1. **Schritt 5 – Bausteine:** Für jedes Feld von `Tenancy`, `Handover`, `Tenant`, `Prospect`, `Viewing` (und die von der
   Vermietung gelesenen Felder von `RentalUnit`) die drei Fragen des Leitfadens beantwortet (technisch? eigene
   Identität? nur durch Werte bestimmt?). Spaltentypen und Pflichtfelder aus den Mapping-Annotationen und der Migration.
   → `fachmodell.md` (Tabellen, Wertobjekte, Aufzählungen, Klassendiagramm).
2. **Schritt 6 – Zustände:** `grep -n "const "` in den Entities und `grep -rn "setStatus("` in `src`; zusätzlich die
   Formulare (`ProspectType`, `RentalUnitType`: Status frei wählbar) und die Bedingungen in den Templates. Jede
   Fundstelle als Übergang eingetragen, Ist- und Soll-Diagramm getrennt. → `verhalten.md` (Befunde B-01 bis B-12).
3. **Schritt 7 – Regeln:** Regelkandidaten aus Übung 3 und die Bedingungen aus Schritt 6 als Vorbedingung,
   Nachbedingung, Invariante formuliert. REGEL-01 überarbeitet, REGEL-15 bis REGEL-29 neu. → `regelkatalog.md`.
4. **Schritt 8 – Aggregate:** Für jede Regel die gleichzeitig geprüften Objekte bestimmt, daraus Aggregate und
   Wurzeln geschnitten. → `fachmodell.md`, Abschnitt »Schritt 8«.

### Entscheidungen

| Nr. | Entscheidung | Begründung | verworfen |
|---|---|---|---|
| E-13 | Schritte 5–8 zunächst nur für die **Vermietung**. REGEL-02 bis REGEL-14 (Meldungen/Aufgaben) bleiben im alten Format. | Für Meldungen/Aufgaben fehlen noch die Schritte 1–4 (Bestand, AF, Glossar); ohne sie gäbe es keine einheitlichen Begriffe für Bausteine und Aggregate. | alle Bereiche gleichzeitig – Begriffe wären uneinheitlich |
| E-14 | Merker `noticeGiven` wird **weggelassen**; nur der Zustand zählt. | Er ist doppelt zum Zustand, wird bei der Kündigung nicht, bei der Aufhebung doch gesetzt (B-05) und widerspricht dem Zustand schon in den Beispieldaten. Ein Zustand genügt – das Flag wird sonst vergessen (VL 4, Folie 19, Alternative B). | beide behalten (A) |
| E-15 | **Übergabe** ist Entität **innerhalb** des Aggregats Mietverhältnis; höchstens ein Einzug und ein Auszug je Mietverhältnis (Art = lokale Identität). | Der Auszug ändert den Zustand des Mietverhältnisses (REGEL-26) – beides muss zusammen geprüft werden. Mehrfache Einzüge (B-06) sind fachlich sinnlos. | Übergabe als eigenes Aggregat; Übergabe als Wertobjekt (ein Protokoll wird nachträglich ergänzt, es ist kein austauschbarer Wert) |
| E-16 | **Mieter** und **Interessent** sind eigene Aggregate mit neuer fachlicher Identität **Mieternummer** bzw. **Interessentennummer**. Das Mietverhältnis verweist nur über Mieternummern. | Ein Mieter kann mehrere Mietverhältnisse haben und wird von Meldungen genutzt. Das Altsystem hat nur die technische id – für einen Verweis über die Identität (VL 4, Folie 23) braucht es eine fachliche Nummer. **Annahme**, F-16. | Mieter im Aggregat Mietverhältnis; technische id als Identität |
| E-17 | **Mieteinheit** ist ein eigenes Aggregat (Bestand); sie reagiert auf die Ereignisse des Mietverhältnisses (REGEL-19). | Die Einheit hat eigene Zustände (Renovierung, gesperrt) und Regeln. Ein kurzes Zeitfenster bei der Umstellung ist für die Hausverwaltung unkritisch. | Alternative A (Einheit im Aggregat Mietverhältnis) – das Aggregat würde bei jeder Renovierung berührt |
| E-18 | Name von Interessent und Mieter als Wertobjekt **Personenname** (Anrede, Vorname, Nachname); Mieter erhält Verweis **Herkunft** auf den Interessenten. | Die Zusage teilt den Namen am letzten Leerzeichen (»Anna Maria von Berg« → Vorname »Anna Maria von«). Die Herkunft steht heute nur als Freitext in den Notizen. | Name als ein Text beim Interessenten lassen |
| E-19 | **Besichtigung** gehört zum Aggregat **Interessent**. | REGEL-28 ändert Besichtigung und Zustand des Interessenten gemeinsam; ein Interessent hat nur wenige Besichtigungen. | eigenes Aggregat Besichtigung mit Ereignis an den Interessenten |
| E-20 | REGEL-01: Mietverhältnis auch für eine **reservierte** Einheit – aber nur für die **eigene** Reservierung. Die Reservierung merkt sich den Mieter. | Weg über die Zusage braucht »reserviert« (F-02); ohne Bindung an den Mieter könnte jeder die reservierte Einheit vermieten → doppelte Verträge (VL 4, Folie 19). | nur »frei« (Template) – bricht den Weg über die Zusage; »frei oder reserviert« ohne Bindung (Controller) |
| E-21 | Kante **aktiv → beendet** (Auszug ohne Kündigung) nur für **befristete** Mietverhältnisse nach Ablauf des Mietendes. | Die Kante ist ein Befund des Altsystems (UE 04, B-01). Ein befristetes Mietverhältnis endet aber ohne Kündigung – dafür braucht es den Weg. | Kante ganz streichen – befristete Verträge könnten dann nie enden |
| E-22 | Aufhebung: **Rolle Administration** und Zustand **aktiv/gekündigt** sind Vorbedingungen im Modell. | Heute nur im Template geprüft (B-02); die neue Schnittstelle muss die Rolle erzwingen (VL 4, Folie 19, Alternative B). | so lassen (A) |
| E-23 | **Kündigungsfrist** ist ein benannter Wert im Modell (heute 3 Monate), nicht eine Zahl im Controller. Eine Kündigung verlängert ein befristetes Mietverhältnis nicht. | Die Frist ist nirgends sichtbar; der ungenutzte Parameter `app.notice_period_months` zeigt, dass sie einstellbar sein sollte (VL 4, Folie 18). Verlängerung durch Kündigung (B-08) ist fachlich widersinnig – **Annahme**, F-17. | Frist fest in die Regel schreiben |
| E-24 | REGEL-21 gilt für **laufend** (aktiv und gekündigt), nicht nur für aktiv. | Ein gekündigtes Mietverhältnis läuft noch und braucht einen Vertragspartner (F-10). | Altsystem übernehmen (nur aktiv) |
| E-25 | Interessent: Zustand ist **kein Formularfeld** mehr; neue Befehle »Bewerbung erfassen« und »absagen«; eine Besichtigung setzt den Zustand nicht zurück. | Frei wählbarer Zustand (B-09) und Rücksetzen durch die Besichtigung (B-10) machen den Stand der Anfrage unzuverlässig. Überholt E-04 für das Zielsystem. | Zustand weiter frei wählbar |
| E-26 | Neuer Übergang **Entwurf → verworfen** (Annahme). | Ein nie unterschriebener Entwurf hält heute die Einheit für immer reserviert (B-04); Prüffrage Schritt 6 (jeder Zustand braucht einen Weg hinaus) ist sonst für die Einheit »reserviert« nicht erfüllt. | Entwurf löschen (dann ginge die Vertragsnummer verloren) |

### Befunde (Kurzfassung, Details in `verhalten.md`)

- B-01 Auszug beendet auch ein nie gekündigtes Mietverhältnis · B-02 Aufhebung ohne Zustands- und Rollenprüfung ·
  B-03 Auszug bei Entwurf · B-04 Entwurf nicht verwerfbar · B-05 Merker `noticeGiven` widerspricht dem Zustand ·
  B-06 beliebig viele Einzüge · B-07 befristete Verträge bleiben nach Ablauf aktiv · B-08 Kündigung verlängert befristete Verträge ·
  B-09 Zustand des Interessenten frei wählbar · B-10 Besichtigung setzt den Interessenten zurück · B-11 doppelte Zusage → doppelter Mieter ·
  B-12 Bewerbung und Absage ohne eigene Aktion.
- Weitere Funde beim Präzisieren der Regeln: Aufhebung kann ein Mietende vor dem Mietbeginn erzeugen (REGEL-16);
  zwei Entwürfe für dieselbe Einheit sind möglich (REGEL-15); ein Nachmieter-Vertrag während der Kündigungsfrist ist
  unmöglich (REGEL-15); Betrag 0 wird als »nicht angegeben« überschrieben (REGEL-18).

### Offene Fragen an den Fachbereich (neu)

| Nr. | Frage | betrifft |
|---|---|---|
| F-11 | Wie wird ein Entwurf verworfen, der nie unterschrieben wird? Heute bleibt die Einheit dauerhaft reserviert. | AF-05, E-26 |
| F-12 | Darf während der Kündigungsfrist schon das Mietverhältnis des Nachmieters angelegt werden (ohne Überschneidung der Laufzeiten)? | AF-05, REGEL-01, REGEL-15 |
| F-13 | Gilt ein aufgehobenes Mietverhältnis bis zum Auszug noch als »laufend« (Sollstellungen, Liste »laufende«)? | AF-09, Glossar »laufend« |
| F-14 | Sollen Zählerstände strukturiert erfasst werden (Zähler, Stand, Ablesedatum) statt als Freitext? | AF-10 |
| F-15 | Darf die Kaution höher sein als drei Kaltmieten? Gilt eine Obergrenze auch für Gewerbe? | AF-05, REGEL-18 |
| F-16 | Gibt es schon eine fachliche Mieter- oder Interessentennummer (z. B. in der Buchhaltung), die wir übernehmen sollten? | E-16 |
| F-17 | Was passiert bei der Kündigung eines befristeten Mietverhältnisses – gilt das frühere Ende? | AF-08, REGEL-23 |

### Offene Aufgaben

- [ ] `fachmodell.pdf`, `verhalten.pdf`, `regelkatalog.pdf` aus den Markdown-Dateien exportieren (Mermaid-Diagramme vorher in der Vorschau prüfen).
- [ ] Schritte 1–8 für **Bestand** und **Meldungen/Aufgaben**; dabei REGEL-02 bis REGEL-14 ins erweiterte Format bringen.
- [ ] Glossar um die neuen Begriffe dieser Übung ergänzt – vom Team gegenlesen.
- [ ] Befunde B-01 bis B-03 und B-10/B-11 in der laufenden Anwendung nachklicken (direkter Aufruf der Route, z. B. per Browser-Entwicklerwerkzeug).
- [ ] Abschlusscheck P1: Kann eine andere Gruppe mit `model/` erklären, wie »Mietverhältnis kündigen« abläuft?

### KI-Einsatz

- Werkzeug: Claude (Anthropic).
- Wofür: Entwurf von `fachmodell.md`, `verhalten.md`, der erweiterten Regeln im `regelkatalog.md` und dieses Eintrags –
  aus Code (Entities, Services, Controller, Formulare, Templates, Repository, `config/services.yaml`), VL 4, UE 04 und Leitfaden.
- Was wir selbst geprüft haben: *(von der Gruppe ausfüllen – z. B. welche Fundstellen nachgelesen, welche Entscheidungen geändert)*
- Grenzen: Die KI hat den Code gelesen, die Anwendung aber nicht ausgeführt. Mieternummer, Interessentennummer,
  »Entwurf verwerfen« und die Regeln zu befristeten Verträgen sind **Annahmen** und mit dem Fachbereich zu klären.
