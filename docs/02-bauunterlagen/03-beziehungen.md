# Beziehungsdiagramm und Bezugsformeln

**26 Beziehungen, 33 Tabellenauftreten, 7 Anker.**

## Grundsatz: filtern im Portal, nicht im Diagramm

Ein Portal, das nur offene Hinweise der eigenen Stufe zeigen soll, lässt sich auf
zwei Wegen bauen: über ein zusätzliches gefiltertes Tabellenauftreten, oder über
*Portal einrichten ▸ Portaldatensätze filtern*.

**Hier immer der zweite Weg.** Er spart pro Filter ein Tabellenauftreten samt
Hilfsfeldern, hält das Diagramm lesbar und ist schneller gebaut. Die
Filterformeln stehen in [`04-formeln.md`](04-formeln.md) unter F-20.

Eigene Tabellenauftreten gibt es nur dort, wo FileMaker sie zwingend braucht:
für `Sum()`- und `Count()`-Berechnungen und für die Navigation in Skripten.

## Anordnung im Diagramm

Sieben Anker nebeneinander, die zugehörigen Auftreten jeweils darunter
(Anchor-Buoy). Kein Auftreten wird über Ankergrenzen hinweg wiederverwendet —
auch wenn es technisch ginge. Der Graph wird dadurch breiter, aber man sieht
jedem Layout an, aus welchem Kontext es kommt.

```
PROJEKT      ABSCHNITT    SCHRITT    DOKUMENT    MANUSKRIPT   VORLAGE    PERSON
Projects     Sections     Steps      Documents   Manuscripts  Section-   People
 ├Sections    ├Steps       └Check-    └Document-   └Submis-    Templates   ├Project-
 ├Steps       ├Check-        list-      Versions     sions      ├Step-     │ People
 ├Checklist-  │ Items        Items                              │Templates └Tasks
 │ Items      ├Deliver-                                         └Checklist-
 ├Deliver-    │ ables                                            Templates
 │ ables      ├Tasks
 ├Tasks       ├Projects
 ├Documents   └zz_Utility
 ├Risks
 ├Manuscripts
 ├Nudges
 ├ActivityLog
 ├Project-
 │ People
 │ ├People
 │ └Roles
 └zz_Utility
```

---

## Anker 1 — PROJEKT

Kontext der Layouts `01 Mein Projekt`, `02 Meine Projekte`, `03 Projektakte`,
`05 Zeitachse`, `06 Projektauswahl`.

| Nr | Auftreten | Bezugsformel | Optionen |
|---|---|---|---|
| B-01 | `Projects` | *Anker* | |
| B-02 | `Projects_Sections` | `Projects::__pkProjectID = Sections::_fkProjectID` | Erstellen ☑ Löschen ☑ · sortiert nach `Nr` aufsteigend |
| B-03 | `Projects_Steps` | `Projects::__pkProjectID = Steps::_fkProjectID` | Erstellen ☑ Löschen ☑ · sortiert nach `Nr` |
| B-04 | `Projects_ChecklistItems` | `Projects::__pkProjectID = ChecklistItems::_fkProjectID` | Erstellen ☑ Löschen ☑ · sortiert nach `Nr` |
| B-05 | `Projects_Deliverables` | `Projects::__pkProjectID = Deliverables::_fkProjectID` | Erstellen ☑ Löschen ☑ |
| B-06 | `Projects_Tasks` | `Projects::__pkProjectID = Tasks::_fkProjectID` | Erstellen ☑ Löschen ☑ · sortiert nach `Termin` |
| B-07 | `Projects_Documents` | `Projects::__pkProjectID = Documents::_fkProjectID` | Erstellen ☑ Löschen ☑ |
| B-08 | `Projects_Risks` | `Projects::__pkProjectID = Risks::_fkProjectID` | Erstellen ☑ Löschen ☑ |
| B-09 | `Projects_Nudges` | `Projects::__pkProjectID = Nudges::_fkProjectID` | Erstellen ☑ Löschen ☐ · sortiert nach `Rang` |
| B-10 | `Projects_ActivityLog` | `Projects::__pkProjectID = ActivityLog::_fkProjectID` | Erstellen ☑ Löschen ☐ · sortiert nach `Zeitstempel` **absteigend** |
| B-11 | `Projects_Manuscripts` | `Projects::__pkProjectID = Manuscripts::_fkProjectID` | Erstellen ☑ Löschen ☑ |
| B-12 | `Projects_ProjectPeople` | `Projects::__pkProjectID = ProjectPeople::_fkProjectID` | Erstellen ☑ Löschen ☑ |
| B-13 | `Projects_ProjectPeople_People` | `ProjectPeople::_fkPersonID = People::__pkPersonID` | |
| B-14 | `Projects_ProjectPeople_Roles` | `ProjectPeople::_fkRoleID = Roles::__pkRoleID` | |
| B-15 | `Projects_zz_Utility` | `Projects × zz_Utility` — **Operator ×** (kartesisch) | liefert immer den einen Einstellungsdatensatz |

> **B-03 und B-04 sind der Grund für die redundanten Fremdschlüssel** in `Steps`
> und `ChecklistItems`. Ohne sie bräuchte Bildschirm 01 eine dreistufige
> Bezugskette, und der Portalfilter würde unübersichtlich. Der Preis: Skript S-03
> muss `_fkProjectID` beim Anlegen mitschreiben — drei zusätzliche Zeilen.

> **Zu B-15:** Der Operator `×` verbindet jeden Datensatz mit jedem. Da
> `zz_Utility` genau einen Datensatz hat, ist das der einfachste Weg an die
> Einstellungen. Im Beziehungsdiagramm den Operator im Aufklappmenü zwischen den
> beiden Feldern auf `×` stellen.

---

## Anker 2 — ABSCHNITT

Kontext von Layout `04 Abschnitt`.

| Nr | Auftreten | Bezugsformel | Optionen |
|---|---|---|---|
| B-16 | `Sections` | *Anker* | |
| B-17 | `Sections_Steps` | `Sections::__pkSectionID = Steps::_fkSectionID` | Erstellen ☑ Löschen ☑ · sortiert nach `Nr` |
| B-18 | `Sections_ChecklistItems` | `Sections::__pkSectionID = ChecklistItems::_fkSectionID` | Erstellen ☑ Löschen ☑ |
| B-19 | `Sections_Deliverables` | `Sections::__pkSectionID = Deliverables::_fkSectionID` | Erstellen ☑ Löschen ☑ |
| B-20 | `Sections_Tasks` | `Sections::__pkSectionID = Tasks::_fkSectionID` | Erstellen ☑ Löschen ☑ |
| B-21 | `Sections_Projects` | `Sections::_fkProjectID = Projects::__pkProjectID` | Rückweg zum Projekt |
| B-22 | `Sections_zz_Utility` | `Sections × zz_Utility` — **Operator ×** | für die Zeitachsen-Formeln |

---

## Anker 3 — SCHRITT

Nur für die Summenberechnungen F-12 und F-13.

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-23 | `Steps` | *Anker* |
| B-24 | `Steps_ChecklistItems` | `Steps::__pkStepID = ChecklistItems::_fkStepID` · Erstellen ☑ Löschen ☑ · sortiert nach `Nr` |

---

## Anker 4 — DOKUMENT

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-25 | `Documents` | *Anker* |
| B-26 | `Documents_DocumentVersions` | `Documents::__pkDocumentID = DocumentVersions::_fkDocumentID` · Erstellen ☑ · sortiert nach `Version` absteigend |

## Anker 5 — MANUSKRIPT

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-27 | `Manuscripts` | *Anker* |
| B-28 | `Manuscripts_Submissions` | `Manuscripts::__pkManuscriptID = Submissions::_fkManuscriptID` · Erstellen ☑ · sortiert nach `Runde` |

---

## Anker 6 — VORLAGE

Nur von Skript S-03 benutzt, dafür aber tragend: die verschachtelte Bezugskette
macht das Anlegen eines Projekts zu einer einfachen Doppelschleife statt zu
mehreren Suchläufen.

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-29 | `SectionTemplates` | *Anker*, sortiert nach `Nr` |
| B-30 | `SectionTemplates_StepTemplates` | `SectionTemplates::Nr = StepTemplates::SectionNr` · sortiert nach `Nr` |
| B-31 | `SectionTemplates_StepTemplates_ChecklistTemplates` | **zwei Bedingungen:**<br>`StepTemplates::SectionNr = ChecklistTemplates::SectionNr`<br>`StepTemplates::Nr = ChecklistTemplates::StepNr`<br>sortiert nach `Nr` |

> B-31 ist die einzige Beziehung mit zwei Bedingungen. Im Dialog auf *Hinzufügen*
> klicken, um das zweite Feldpaar zu ergänzen. Ohne die zweite Bedingung würden
> alle Checklistenpunkte aller Schritte einer Abschnittsnummer erscheinen.

---

## Anker 7 — PERSON

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-32 | `People` | *Anker* |
| B-33 | `People_Tasks` | `People::__pkPersonID = Tasks::_fkPersonID` · sortiert nach `Termin` |

---

## Was bewusst **keine** Beziehung ist

| Anforderung | Stattdessen |
|---|---|
| „Nur Hinweise meiner Stufe" | Portalfilter F-20.1 |
| „Nur offene Hinweise" | Portalfilter F-20.1 |
| „Nur meine Aufgaben" | Portalfilter F-20.2 über `g_MeinePersonID` |
| „Nur der aktuell laufende Abschnitt" | Portalfilter F-20.3 über `Status = "läuft"` |
| „Nur Schritte, die zutreffen" | Portalfilter F-20.4 über `TrifftNichtZu = 0` |
| „Nur überfällige Aufgaben" | Portalfilter F-20.5 |
| „Meine Projekte" (Leitungsliste) | Suchlauf in Skript S-02, kein Bezug |

Sieben gefilterte Ansichten, null zusätzliche Tabellenauftreten. Das ist der
größte Einzelposten an gesparter Klickarbeit im ganzen Aufbau.

---

## Reihenfolge beim Bauen

1. Alle 20 Tabellen importieren (Doc 07) — FileMaker legt dabei je ein
   Tabellenauftreten mit dem Tabellennamen an
2. Die sieben Anker aus diesen Auftreten heraussuchen und nebeneinander legen
3. Weitere Auftreten über *Duplizieren* erzeugen und nach obiger Liste umbenennen
4. Beziehungen ziehen, Formeln und Optionen nach Tabelle setzen
5. Erst danach die Berechnungsfelder aus Doc 04 anlegen — sie greifen teils auf
   Bezüge zu und lassen sich vorher nicht speichern
