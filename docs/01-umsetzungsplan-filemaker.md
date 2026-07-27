# Umsetzungsplan: Forschungsprojekt-Management-Tool in FileMaker

Grundlage: [`00-kompendium-fachspezifikation.md`](00-kompendium-fachspezifikation.md)

Dieses Dokument beschreibt **Konzept und Bau-Reihenfolge**. Es ist bewusst kein
vollständiges Feldverzeichnis — die Detailspezifikation entsteht etappenweise
direkt vor der jeweiligen Umsetzung.

---

## 1. Rahmen der ersten Ausbaustufe

| Entscheidung | Festlegung |
|---|---|
| Plattform | FileMaker Pro Clients + **FileMaker Server** (Mehrbenutzer) |
| Mindestversion | FileMaker 2023 (v20) oder neuer — wegen JSON-, UUID- und Container-Funktionen |
| Datenumfang | **Nur Steuerungs- und Metadaten.** Keine Fallmatrix, keine personenbezogenen Studiendaten |
| Funktionsumfang | **MVP nach Kompendium Kapitel 14.1** |
| Vorgehen | Etappenweise, jede Etappe für sich lauffähig und abnehmbar |

### Was im MVP enthalten ist

Projektstammdaten · Phasen-Engine · Deliverables · Tasks · Dokumente mit
Versionierung · Risiko- und Blocker-Modul · Basis-Dashboard · Publikationsstatus
· Audit-Log · Rollen- und Rechtekonzept.

### Was bewusst später kommt

Variablenregister und Codebook (§9.9) · Analysepakete (§9.10) · Change Requests
· Lessons Learned · vollständige EthicsSubmissions-Historie · Schnittstellen zu
Literaturverwaltung oder klinischen Datenquellen · Projekttemplates je Fachgebiet.

### Eine Ausnahme von der MVP-Grenze

Das Kompendium formuliert in §10 die harte Regel *„keine Datenerhebung ohne
regulatorische Freigabe"*. Diese Regel lässt sich nicht durchsetzen, wenn der
Freigabestatus nirgends steht. Deshalb wandern **fünf Ethik-Felder direkt in die
Projekttabelle** (Ethikpflicht ja/nein, Aktenzeichen, Einreichungsdatum,
Freigabedatum, Geltungsbereich). Die vollständige `EthicsSubmissions`-Tabelle mit
Rückfragen- und Amendment-Historie folgt in Stufe 2 — die Felder werden dann
migriert, nicht neu erfunden.

---

## 2. Grundsatzentscheidungen für FileMaker

Diese Entscheidungen prägen alles Weitere und sind nachträglich teuer zu ändern.

### 2.1 Eine Datei, nicht mehrere

Da keine Patientendaten gespeichert werden, gibt es keinen Grund zur
Dateitrennung. **Eine `.fmp12`-Datei** hält Schema, Daten und Oberfläche. Falls
später identifizierende Daten dazukommen, wird eine zweite Datei angehängt — die
UUID-Schlüssel (siehe 2.3) machen das problemlos.

### 2.2 Anchor-Buoy im Beziehungsdiagramm

Pro Kontext (Projekt, Phase, Deliverable, Task, Dokument, Risiko, Manuskript) ein
eigener Anker mit den daran hängenden Tabellenauftreten. Kein
Beziehungsdiagramm-Spaghetti, keine Mehrfachnutzung von Tabellenauftreten über
Kontexte hinweg. Der Graph wird dadurch groß, aber lesbar und wartbar.

### 2.3 UUID statt fortlaufender Nummer

Alle Primärschlüssel als `Get(UUID)` mit Auto-Eingabe, nicht änderbar.
Grund: Import von Templates, spätere Zusammenführung von Dateien und
Datensatz-Duplizierung bleiben konfliktfrei. Fortlaufende Nummern gibt es
zusätzlich nur dort, wo Menschen sie lesen (Projektcode wie `2026-014`).

### 2.4 Namenskonvention

```
__pkProjectID      Primärschlüssel
_fkProjectID       Fremdschlüssel
Status             normales Feld
c_PhaseAging       Berechnungsfeld (unstored)
s_PhaseAging       per Script gesetzter, gespeicherter Wert
g_CurrentProjectID globales Feld
zz_Utility         Hilfstabelle
```

Konsequent durchgehalten, ohne Ausnahmen. Das spart bei jeder späteren
Erweiterung Suchzeit.

### 2.5 Status wird nie direkt bearbeitet

**Zentrale Designentscheidung.** Statusfelder sind auf allen Layouts
schreibgeschützt. Jeder Statuswechsel läuft ausschließlich über ein Script mit
Button. Das Script prüft die Geschäftsregel, führt sie aus oder verweigert sie
mit Begründung, und schreibt den Audit-Eintrag.

Nur so sind die Regeln aus §10 überhaupt durchsetzbar. Ein Popup-Feld mit
Werteliste lässt sich immer umgehen; ein Script nicht.

### 2.6 Kriterien sind Datensätze, keine Textfelder

Entry- und Exit-Kriterien einer Phase liegen als eigene Tabelle
(`PhaseCriteria`) vor: ein Datensatz pro Kriterium, mit Typ (Entry/Exit),
Pflichtkennzeichen, Erfüllt-Flag, Erfüllt-von und Erfüllt-am.

Als Freitextfeld wären Exit-Kriterien dekorativ. Als Datensatzliste sind sie
maschinell prüfbar — das Abschluss-Script zählt einfach die offenen Pflicht-
kriterien. Das ist der Unterschied zwischen einer Phasen-Engine und einer
Notizzettel-Datenbank.

### 2.7 Kennzahlen vorberechnen, nicht live rechnen

Ungespeicherte Berechnungsfelder (Phase-Aging, Ampelstatus, Plan-Ist-Abweichung)
sind in Listenansichten und Portalen langsam und lassen sich weder sortieren noch
suchen. Deshalb zweigleisig:

- **Detailansicht:** ungespeicherte Berechnung, immer aktuell.
- **Dashboard und Listen:** ein nächtliches Server-Script schreibt dieselben
  Werte in gespeicherte `s_`-Felder. Sortierbar, suchbar, schnell.

### 2.8 Audit-Log von Anfang an

Eine `AuditLog`-Tabelle mit Zeitstempel, Konto, Tabelle, Datensatz-UUID, Feld,
Alt-Wert, Neu-Wert, Auslöser.

Beschrieben wird sie primär durch die Statuswechsel- und Freigabe-Scripts.
Zusätzlich ein `OnRecordCommit`-Trigger auf den Kernlayouts, der einen
JSON-Schnappschuss der überwachten Felder vergleicht.

**Bekannte Grenze:** Script-Trigger hängen am Layout. Änderungen per Import oder
über ein Layout ohne Trigger werden nicht protokolliert. Deshalb gilt als Regel:
Datenänderungen laufen über die dafür vorgesehenen Layouts, Importe nur durch die
Administration und mit eigenem Log-Eintrag.

### 2.9 Entwickeln mit dem Data Migration Tool

Sobald das System produktiv Daten enthält, wird nicht mehr an der Live-Datei
geschraubt. Ablauf: Entwicklungskopie ändern → `FMDataMigration` überträgt die
Produktivdaten in die neue Struktur → Austausch im Wartungsfenster. Das gehört ab
Etappe 1 zur Routine, nicht erst wenn es weh tut.

---

## 3. Datenmodell des MVP

### 3.1 Tabellen

**Stammdaten**
- `Projects` — Projektakte inkl. der fünf Ethik-Felder
- `ProjectTypes` — Studiendesign/Vorhabentyp, steuert die Phasenvorlage
- `People` — Personenstammdaten
- `Roles` — Rollentypen (PI, Projektführung, Datenerheber, Statistik, Regulatory, Co-Autor, Koordination)
- `ProjectPeople` — Zuordnung Person ↔ Projekt ↔ Rolle, inkl. RACI-Kennzeichen

**Phasen-Engine**
- `PhaseTemplates` — Katalog der Standardphasen je Projekttyp
- `Phases` — Phaseninstanzen je Projekt
- `PhaseCriteria` — Entry-/Exit-Kriterien als prüfbare Datensätze

**Arbeitsebene**
- `Deliverables` + `DeliverableTemplates`
- `Tasks`

**Nachweise und Steuerung**
- `Documents` + `DocumentVersions`
- `Risks`
- `Manuscripts` + `Submissions`
- `AuditLog`
- `zz_Utility` — ein Datensatz, hält globale Felder und Systemeinstellungen

### 3.2 Kernbeziehungen

```
ProjectTypes ──< Projects ──< Phases ──< Deliverables ──< Tasks
                    │            └──< PhaseCriteria
                    ├──< ProjectPeople >── People >── Roles
                    ├──< Documents ──< DocumentVersions
                    ├──< Risks
                    └──< Manuscripts ──< Submissions
```

`Tasks` hängen laut §9.5 an einer Phase **oder** einem Deliverable. Umsetzung:
zwei Fremdschlüssel, wobei `_fkPhaseID` immer gefüllt ist und `_fkDeliverableID`
optional. So bleibt jede Aufgabe eindeutig einer Phase zugeordnet und das
Phasen-Fortschrittsmaß bleibt berechenbar.

### 3.3 Template-Mechanik

`PhaseTemplates` und `DeliverableTemplates` sind Vorlagenkataloge, keine
Projektdaten. Beim Anlegen eines Projekts kopiert ein Script anhand des
Projekttyps die passenden Vorlagen in `Phases`, `PhaseCriteria` und
`Deliverables`. Ab dann sind die Instanzen unabhängig — eine spätere
Vorlagenänderung verändert laufende Projekte nicht.

Das erfüllt §12 („Automatische Anlage von Standardphasen bei Projektstart") und
hält gleichzeitig die Nachvollziehbarkeit sauber.

---

## 4. Geschäftsregeln und ihre technische Verankerung

| Regel (§10) | Umsetzung |
|---|---|
| Keine Phasenaktivierung ohne Entry-Kriterien | Script `Phase_Aktivieren` zählt offene Pflicht-Entry-Kriterien |
| Kein Phasenabschluss ohne Exit-Kriterien und Deliverables | Script `Phase_Abschliessen` prüft Kriterien **und** Deliverable-Status |
| Keine Datenerhebung ohne regulatorische Freigabe | `Phase_Aktivieren` sperrt die Phase „Datenerhebung", solange `Ethikpflicht = ja` und `Freigabedatum` leer ist |
| Kein Statuswechsel unter Umgehung der Regeln | Statusfelder layoutseitig gesperrt, Wechsel nur per Script (2.5) |
| Eskalation bei ausbleibendem Statusupdate | Server-Script prüft nächtlich `Letztes Update` gegen die Frist |
| Risiken mit festem Review-Termin | Pflichtfeld `Review-Datum`, Server-Script meldet Überschreitung |
| Audit-Log bei relevanten Änderungen | Scripts + Commit-Trigger (2.8) |

Die Regeln aus §10, die Analyse und Change Requests betreffen, greifen erst mit
den Modulen der Stufe 2.

---

## 5. Rollen und Rechte

**Fünf Privilege Sets:**

| Set | Umfang |
|---|---|
| Administration | Vollzugriff inkl. Schema und Vorlagenpflege |
| PI / Betreuung | Alle Projekte lesen, Freigaben erteilen, Phasen abschließen |
| Projektführung | Eigene Projekte voll bearbeiten, keine Freigaben |
| Mitarbeit | Tasks und Deliverables der zugewiesenen Projekte bearbeiten |
| Lesen | Nur Ansicht und Export |

Das Kompendium fordert in §8.2 vier Rechteebenen (Lesen, Bearbeiten, Freigeben,
Administrieren). Diese Ebenen bilden die Sets ab; „Freigeben" ist dabei kein
FileMaker-Recht, sondern die Berechtigung, die Freigabe-Scripts auszuführen —
geprüft über `Get(AccountPrivilegeSetName)`.

**Datensatzbezogene Einschränkung** (nur eigene Projekte sehen) ist in FileMaker
über berechnete Zugriffsrechte möglich, kostet aber Performance und erschwert
Auswertungen. Für das MVP deshalb: Sichtbarkeit über gefilterte Layouts und
Suchen steuern, echte Record-Level-Access-Regeln erst bei belegtem Bedarf.

Die RACI-Zuordnung aus §8.1 liegt fachlich in `ProjectPeople` und ist unabhängig
vom technischen Rechtesystem — sie beantwortet „wer ist zuständig", nicht „wer
darf klicken".

---

## 6. Oberfläche

Ein Layout-Set pro Kontext, konsistent aufgebaut:

- **Portfolio-Cockpit** — Listenansicht aller Projekte mit Ampel, aktueller
  Phase, nächstem Meilenstein, Blockerzahl (§9.1). Arbeitet auf den
  vorberechneten `s_`-Feldern.
- **Projektakte** — Kopfbereich mit Stammdaten, darunter Registerkarten für
  Phasen, Deliverables, Tasks, Dokumente, Risiken, Publikation.
- **Phasen-Detail** — Kriterienliste, Deliverables, Statusbuttons.
- **Arbeitslisten** — „Meine Tasks", „Fällig in 30/60/90 Tagen", „Aktive
  Blocker", „Wartet auf Freigabe".
- **Dialoge** als Card-Fenster, nicht als eigene Layouts im Fenster.

Bedienregel: Statuswechsel immer als beschrifteter Button mit Rückmeldung, nie
als stilles Feld.

---

## 7. Automationen auf dem Server

Ein nächtlicher Server-Zeitplan („Nachtlauf") führt aus:

1. Kennzahlen neu berechnen und in die `s_`-Felder schreiben
2. Ampelstatus je Projekt setzen
3. Überfällige Deliverables und Tasks markieren
4. Projekte ohne Statusupdate innerhalb der Frist eskalieren
5. Fällige Risiko-Reviews melden
6. Benachrichtigungsmail an Verantwortliche versenden

Zusätzlich ein wöchentlicher Lauf für den Portfolio-Report an die Betreuung.

Rechenintensive Aktionen aus dem Client (Projektanlage aus Vorlage,
Dashboard-Aufbau) laufen über *Perform Script on Server*, nicht lokal.

---

## 8. Bau-Reihenfolge

Jede Etappe endet mit einem lauffähigen Stand und einem prüfbaren Ergebnis.

### Etappe 0 — Fundament
Datei anlegen, Namenskonvention festschreiben, `zz_Utility` mit globalen Feldern,
Konten und die fünf Privilege Sets, Server-Ablage, Sicherungsplan, EAR aktivieren.
*Abnahme:* Datei liegt auf dem Server, Anmeldung mit jedem Set funktioniert.

### Etappe 1 — Stammdaten
`Projects`, `ProjectTypes`, `People`, `Roles`, `ProjectPeople`. Pflichtfelder
nach §9.2. Projektakte-Layout mit Kopfbereich und Beteiligtenliste.
`AuditLog`-Tabelle und Protokoll-Script als Infrastruktur.
*Abnahme:* Ein Projekt lässt sich vollständig anlegen, Beteiligte zuordnen,
Änderungen erscheinen im Audit-Log.

### Etappe 2 — Phasen-Engine *(Kernstück)*
`PhaseTemplates`, `Phases`, `PhaseCriteria`. Die zwölf Standardphasen aus §6.1 als
Vorlage. Scripts `Projekt_Initialisieren`, `Phase_Aktivieren`,
`Phase_Abschliessen`, `Phase_Blockieren` mit vollständiger Regelprüfung.
*Abnahme:* Neues Projekt erzeugt automatisch alle Phasen; eine Phase lässt sich
nachweislich nicht abschließen, solange ein Pflicht-Exit-Kriterium offen ist.

### Etappe 3 — Deliverables
`Deliverables`, `DeliverableTemplates`, Freigabe-Workflow mit Owner und Reviewer,
Verknüpfung in die Phasen-Abschlussprüfung.
*Abnahme:* Phasenabschluss scheitert bei nicht freigegebenem Pflicht-Deliverable.

### Etappe 4 — Tasks
`Tasks` mit Zuordnung, Termin, Priorität, Abhängigkeiten, Kommentarverlauf.
Arbeitsliste „Meine Tasks".
*Abnahme:* Aufgaben sind zuweisbar, Fortschritt schlägt auf die Phase durch.

### Etappe 5 — Dokumente und Versionen
`Documents` + `DocumentVersions`, Container mit External Secure Storage,
Freigabestatus, Historie, Verknüpfung zu Projekt/Phase/Deliverable.
*Abnahme:* Eine neue Version verdrängt die alte nicht, sondern ergänzt sie; die
Historie ist vollständig nachvollziehbar.

### Etappe 6 — Risiken, Blocker, Ethik-Sperre
`Risks` nach §9.7, Blockerkennzeichen auf Phasenebene, Aktivierung der
Ethik-Sperrregel aus §9.8.
*Abnahme:* Ein freigabepflichtiges Projekt ohne Freigabedatum kann die
Datenerhebungsphase nicht starten.

### Etappe 7 — Publikationsstatus
`Manuscripts` und `Submissions` mit den neun Statuswerten aus §9.11,
Autorenreihenfolge, Revisionsrunden.
*Abnahme:* Ein Projekt lässt sich von Draft bis Published durchgehend abbilden.

### Etappe 8 — Dashboard und Automationen
Portfolio-Cockpit, Fristenlisten, Blocker-Übersicht, Publikationspipeline.
Nachtlauf und Wochenreport auf dem Server.
*Abnahme:* Kennzahlen aus §11 stimmen mit den Detaildaten überein; der Nachtlauf
läuft ohne Eingriff durch.

### Etappe 9 — Härtung und Rollout
Rechte-Feinschliff, Validierungsregeln, Export nach Excel/PDF, Testlauf mit zwei
echten Projekten, Kurzdokumentation für Anwender.
*Abnahme:* Produktivfreigabe.

---

## 9. Arbeitsweise

`.fmp12`-Dateien sind Binärformat und lassen sich nur in FileMaker Pro selbst
erzeugen. Die Arbeitsteilung sieht deshalb so aus:

**Im Repository entsteht pro Etappe:**
- die Detailspezifikation (Feldliste mit Typen, Beziehungen, Validierungen)
- alle Berechnungsformeln zum Kopieren
- die Script-Schritte in ausführbarer Reihenfolge
- Layout-Aufbau und Rechtematrix
- Import-fertige CSV-Dateien für Vorlagen und Wertelisten

**In FileMaker Pro passiert:** der Nachbau anhand dieser Anleitung.

Damit ist jede Etappe reproduzierbar, versioniert und überprüfbar — und der
Aufbau bleibt dokumentiert, auch wenn später jemand anderes daran arbeitet.

---

## 10. Offene Punkte vor Etappe 0

1. **FileMaker-Version und Lizenzen** — welche Serverversion steht bereit, wie
   viele gleichzeitige Nutzer?
2. **Projekttypen** — welche Studiendesigns sollen anfangs hinterlegt sein? Davon
   hängen die Phasenvorlagen ab.
3. **Phasenvorlagen** — die zwölf Phasen aus §6.1 für alle Typen gleich, oder je
   Typ abweichend?
4. **Eskalationsfristen** — nach wie vielen Tagen ohne Statusupdate wird
   eskaliert, an wen?
5. **Mailversand** — steht ein SMTP-Zugang für die Benachrichtigungen bereit?
6. **Dokumentablage** — Dateien in FileMaker-Containern oder nur Verweise auf
   einen bestehenden Netzwerkspeicher?

Punkte 2 bis 4 werden spätestens vor Etappe 2 gebraucht, die übrigen vor
Etappe 5 beziehungsweise 8.
