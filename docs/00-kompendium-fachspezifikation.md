# Kompendium: Spezifikation und Bauanleitung für ein spezialisiertes Forschungsprojekt-Management-Tool

## 1. Zweck und Zielbild

Dieses Dokument vereint Spezifikation und Bauanleitung für ein spezialisiertes System zur Planung, Steuerung und Überwachung wissenschaftlicher Forschungsprojekte mit klinischem Schwerpunkt.[cite:16][cite:25] Es richtet sich an Betreuende und Teams, die mehrere Forschungsprojekte parallel führen und dafür ein Werkzeug benötigen, das über klassische Aufgabenverwaltung hinausgeht.[cite:25][cite:30]

Das Zielbild des Tools:

- Abbildung des gesamten Projektlebenszyklus von der Themenidee über Planung, regulatorische Freigaben, Datenerhebung, Bereinigung, Analyse bis zur Manuskripteinreichung und zum formalen Abschluss.[cite:16][cite:25]
- Standardisierung des Forschungsablaufs über alle Projekte hinweg.[cite:25]
- Transparente Steuerung von Zeit, Status, Verantwortlichkeiten, Risiken und Blockern.[cite:25][cite:30]
- Saubere Dokumentation aller wissenschaftlich relevanten Entscheidungen, Datenstände und Publikationsschritte.[cite:22][cite:25]

Eine klinische Untersuchung zeigte eine mediane Gesamtdauer von 18 Monaten von der ersten Idee bis zur Publikation, mit median 2 Monaten für die Projektentwicklung, 6 Monaten für die Datenerhebung, 2 Monaten für das Schreiben und 4 Monaten für die Publikationsphase.[cite:16] Ein geeignetes Tool muss daher Zeit in Phasen, kritische Pfade und Verzögerungsursachen explizit erfassen und steuerbar machen.[cite:16][cite:25]

## 2. Problemdefinition

Generische Projekttools behandeln Forschungsprojekte häufig wie normale Aufgabenpakete. In der Realität bestehen wissenschaftliche Vorhaben aus stark abhängigen Teilprozessen wie Fragestellungsentwicklung, Literaturarbeit, regulatorischer Prüfung, Datenerhebung, Datenbereinigung, Analyse, Manuskriptarbeit und Revision.[cite:16][cite:25]

Projektmanagement-Leitfäden für Forschende betonen, dass der Forschungslebenszyklus mit klaren Phasen, Baselines, Monitoring und Change-Control-Prozessen geführt werden sollte.[cite:25][cite:30] Reproduzierbare Forschung erfordert außerdem dokumentiertes Datenmanagement, versionierte Artefakte und nachvollziehbare Änderungen.[cite:22]

Ohne ein darauf spezialisiertes Tool bleibt der Prozess fragmentiert, schwer vergleichbar und anfällig für versteckte Engpässe.[cite:25]

## 3. Leitprinzipien

Das Tool sollte nach folgenden Prinzipien konstruiert werden:[cite:25]

- **Phasenorientierung** statt reiner Aufgabenorientierung.
- **Pflichtdaten und Exit-Kriterien** pro Phase statt freier Notizzettel-Logik.[cite:25]
- **Trennung von Portfolio-, Projekt-, Phasen-, Deliverable- und Task-Ebene**.[cite:25]
- **Nachvollziehbarkeit aller Änderungen** durch Audit-Logs und Versionierung.[cite:22][cite:25]
- **Frühe Sichtbarkeit von Risiken und Verzögerungen** durch explizite Risikoobjekte.[cite:25]
- **Wiederverwendbare Templates** für häufige Forschungstypen.[cite:25]

## 4. Zielgruppe und Einsatzkontext

Zielgruppe sind Betreuer, Principal Investigators, wissenschaftliche Mitarbeitende, Doktorierende und Koordinationsteams, die mehrere klinische oder akademische Projekte parallel betreuen.[cite:25][cite:30]

Besonders sinnvoll ist das Tool in Umgebungen mit:

- Standardisierten Datenquellen (z.B. Praxissoftware, Register, Scans).[cite:25]
- Wiederkehrenden Studiendesigns.[cite:25]
- Laufenden Publikationspipelines und klaren Jahreszielen.[cite:25][cite:30]

## 5. Systemarchitektur

### 5.1 Ebenenmodell

Die Architektur sollte mehrschichtig aufgebaut sein, um unterschiedliche Steuerungslogiken abzubilden.[cite:25]

Empfohlene Ebenen:

- **Portfolio-Ebene**: Gesamtübersicht aller laufenden und geplanten Projekte.
- **Projekt-Ebene**: Fachliche und organisatorische Projektakte.
- **Phasen-Ebene**: Wissenschaftliche Stationspunkte des Projekts.
- **Deliverable-Ebene**: Prüfbare Ergebnisobjekte je Phase.
- **Task-Ebene**: Operative Einzelaktivitäten.
- **Dokumenten-Ebene**: Versionierte Dateien und Nachweise.[cite:25]

Diese Hierarchie verhindert, dass methodische Meilensteine und einfache Tasks vermischt werden und erlaubt eine klare Steuerung pro Ebene.[cite:25]

### 5.2 Hauptmodule

Die Module entsprechen den Ebenen und ergänzen sie um Risiko-, Regulatorik- und Publikationslogik.[cite:25]

- Portfolio-Modul.
- Projekt-Stammdatenmodul.
- Phasen-Engine.
- Deliverable-Modul.
- Task-Modul.
- Dokumenten- und Versionsmodul.
- Risiko- und Blocker-Modul.
- Regulatorik- und Ethikmodul.
- Datenmanagement-Modul.
- Analyse- und Statistikmodul.
- Publikationsmodul.

## 6. Standardprozess (Fachlogik)

### 6.1 Hauptphasen

Vor der technischen Umsetzung muss der fachliche Zielprozess präzise beschrieben werden.[cite:25][cite:30] Eine kombinierte Struktur aus allgemeinem Projektlebenszyklus und klinischem Forschungsworkflow ist dafür geeignet.[cite:16][cite:25]

Empfohlene Phasen:

1. Themenidentifikation.[cite:25]
2. Machbarkeitsprüfung.[cite:25]
3. Literaturrecherche und Gap-Analyse.[cite:25]
4. Studienprotokoll.[cite:25]
5. Ethik, Datenschutz und regulatorische Freigaben.[cite:25][cite:30]
6. Datensetup.[cite:25]
7. Datenerhebung.[cite:16][cite:25]
8. Datenbereinigung.[cite:22][cite:25]
9. Statistik und Analyse.[cite:25][cite:22]
10. Manuskripterstellung.[cite:16][cite:25]
11. Submission und Revision.[cite:16][cite:25]
12. Abschluss und Lessons Learned.[cite:25][cite:30]

### 6.2 Pflichtdefinition pro Phase

Für jede Phase sind vier Elemente verbindlich festzulegen:[cite:25]

- Ziel der Phase.
- Pflichtdaten.
- Pflichtdokumente.
- Exit-Kriterien.

Beispiele:

- **Themenidentifikation**: Ziel = tragfähiges Vorhaben; Pflichtdaten = Problemstellung, Forschungsfrage, grobe Datenquelle; Exit = Stop/Go-Entscheidung und Portfolio-Priorisierung.[cite:25]
- **Ethikphase**: Ziel = regulatorische Freigabe; Pflichtdokumente = Antragsunterlagen, Protokollversion; Exit = dokumentierte Freigabe mit Geltungsbereich.[cite:16][cite:25]

## 7. Datenmodell

### 7.1 Hauptobjekte

Das Datenmodell sollte relational oder dokumentorientiert so aufgebaut sein, dass Projekte, Personen, Phasen, Risiken, Datenquellen und Publikationsereignisse eindeutig verknüpft sind.[cite:25]

Empfohlene Tabellen/Collections:[cite:25]

| Tabelle | Zweck |
|---|---|
| Projects | Projektstammdaten und Metadaten. |
| ProjectTypes | Typisierung nach Studiendesign oder Vorhabentyp. |
| People | Personenstammdaten. |
| Roles | Rollentypen im System. |
| ProjectPeople | Zuordnungen von Personen zu Projekten und Rollen. |
| Phases | Phaseninstanzen je Projekt. |
| Deliverables | Ergebnisobjekte je Phase. |
| Tasks | Operative Einzelaktivitäten. |
| Milestones | Formale Meilensteine und Deadlines. |
| Risks | Risiken und Blocker. |
| ChangeRequests | Änderungsanträge mit Bewertung und Freigabe. |
| Documents | Dateien, Versionen und Freigabestatus. |
| DataSources | Herkunft der Projektdaten. |
| Variables | Variablenregister und Codebook. |
| EthicsSubmissions | Regulatorische Vorgänge und Entscheidungen. |
| Analyses | Analysepakete und Auswertungsstände. |
| Manuscripts | Manuskriptobjekte und Abschnittsstatus. |
| JournalSubmissions | Einreichungen, Revisionen und Entscheidungen. |
| LessonsLearned | Abschlusswissen und Wiederverwendung. |

### 7.2 Beziehungen

Zentrale Beziehungen:[cite:25]

- Ein Projekt hat viele Phasen.
- Eine Phase hat viele Deliverables.
- Ein Deliverable hat viele Tasks.
- Ein Projekt hat viele Dokumente, Risiken, Änderungen und Publikationsereignisse.
- Ein Projekt hat viele Personen in unterschiedlichen Rollen.

Diese Struktur bildet die reale Komplexität der Forschungsarbeit ab und erlaubt gezielte Auswertungen pro Ebene.[cite:25]

## 8. Rollen- und Rechtekonzept

Forschungsprojekte benötigen klare Verantwortlichkeiten und differenzierte Rechte.[cite:25]

### 8.1 Rollen

Empfohlene Rollen:[cite:25]

- Betreuer/PI.
- Projektführende Person.
- Datenerheber.
- Statistiker.
- Regulatory Owner.
- Co-Autor.
- Studienkoordinator.

Für jedes relevante Objekt (Projekt, Phase, Deliverable, Risiko, Freigabe) sollten RACI-Bezüge gespeichert werden:[cite:25]

- Responsible.
- Accountable.
- Consulted.
- Informed.

### 8.2 Rechte

Mindestens vier Rechtebenen:[cite:25]

- Lesen.
- Bearbeiten.
- Freigeben.
- Administrieren.

Die Kombination von Rollen und Rechten definiert, wer welche Stati ändern, Deliverables freigeben oder Phasen abschließen darf.[cite:25]

## 9. Modul-Spezifikationen

### 9.1 Portfolio-Modul

Das Portfolio-Modul stellt die Gesamtübersicht über alle laufenden und geplanten Projekte bereit.[cite:25]

Anzeigen:

- Projekttitel.
- Projektverantwortliche Person und Betreuer.
- Projekttyp.
- aktuelle Phase.
- Ampelstatus.
- nächster Meilenstein.
- Blocker.
- geplanter Submission-Termin.
- erwartete Publikation.[cite:25][cite:16]

### 9.2 Projekt-Stammdatenmodul

Pflichtfelder:[cite:25]

- Projekttitel.
- Kurzbeschreibung.
- Problemstellung.
- Forschungsfrage.
- Hypothese.
- Projekttyp.
- Fachgebiet.
- geplanter Output.
- Startdatum.
- Zieltermin.
- Priorität.
- Betreuer.
- Projektverantwortliche Person.
- Initialer Status.

Optionale Felder:[cite:25][cite:30]

- Interner Code.
- Förderkontext.
- Verknüpfung zu Dissertation.
- erwarteter Journaltyp.
- Datenquellenkategorie.

### 9.3 Phasen-Engine

Pflichtfelder pro Phaseninstanz:[cite:25]

- Name.
- Reihenfolge.
- Startdatum.
- Zieltermin.
- Enddatum.
- Status.
- verantwortliche Rolle.
- Entry-Kriterien.
- Exit-Kriterien.
- Pflichtfelder.
- Pflichtdokumente.
- Blockerstatus.

Statuswerte:[cite:25]

- Nicht gestartet.
- Aktiv.
- Wartet auf Freigabe.
- Blockiert.
- Abgeschlossen.

Geschäftsregeln:[cite:25]

- Aktivierung nur bei erfüllten Entry-Kriterien.
- Abschluss nur bei erfüllten Exit-Kriterien und vorhandenen Deliverables.
- Phasen mit Regulatorikbezug sperren Folgephasen bei fehlender Freigabe.[cite:16][cite:30]

### 9.4 Deliverable-Modul

Deliverables sind Prüfobjekte zwischen Phase und Task.[cite:25]

Pflichtfelder:[cite:25]

- Titel.
- Typ.
- Zugehörige Phase.
- Owner.
- Reviewer.
- Fälligkeit.
- Status.
- Version.
- Freigabedatum.
- verknüpfte Dokumente.

Typische Deliverables:[cite:25]

- Exposé.
- Literaturmatrix.
- Studienprotokoll.
- Ethikantrag.
- Variablenkatalog.
- Datensatz-Freeze.
- Statistikmemo.
- Manuskriptentwurf.
- Cover Letter.
- Rebuttal Letter.

### 9.5 Task-Modul

Tasks bilden die operative Ebene und hängen immer an einer Phase oder einem Deliverable.[cite:25]

Pflichtfelder:[cite:25]

- Titel.
- Kurzbeschreibung.
- Zuordnung.
- zuständige Person.
- Termin.
- Priorität.
- Status.
- Abhängigkeiten.
- Kommentarverlauf.

Optionale Felder:

- geschätzter Aufwand.
- tatsächlicher Aufwand.
- Wiederholungsvorlage.
- Checkliste.[cite:25]

### 9.6 Dokumenten- und Versionsmodul

Notwendige Funktionen:[cite:22][cite:25]

- Versionierte Ablage.
- Dokumenttyp-Klassifikation.
- Freigabestatus.
- Historie mit Benutzer und Zeitstempel.
- Verknüpfung mit Projekt, Phase, Deliverable.

Dies ist entscheidend für Reproduzierbarkeit, da Änderungen an Artefakten nachvollziehbar dokumentiert werden müssen.[cite:22]

### 9.7 Risiko- und Blocker-Modul

Risiken und Blocker werden separat von Tasks geführt.[cite:25]

Pflichtfelder:[cite:25]

- Kategorie.
- Beschreibung.
- Ursache.
- Eintrittswahrscheinlichkeit.
- Auswirkung.
- Gegenmaßnahme.
- Verantwortlicher.
- Review-Datum.
- Eskalationsstatus.

Typische Kategorien:[cite:25]

- Ethik.
- Datenzugang.
- Datenqualität.
- Ressourcen.
- Statistik.
- Co-Autoren.
- Submission.

### 9.8 Regulatorik- und Ethikmodul

Pflichtfelder:[cite:16][cite:25][cite:30]

- Ethikpflicht ja/nein.
- erforderliche Unterlagen.
- Einreichungsdatum.
- Rückfragenstatus.
- Freigabedatum.
- Gültigkeitsbereich.
- Bezug zu Protokollversion.

Regel:

- freigabepflichtige Projekte dürfen Datenerhebung erst nach dokumentierter Freigabe starten.[cite:16][cite:30]

### 9.9 Datenmanagement-Modul

Kernfunktionen:[cite:22][cite:25]

- Variablenregister mit Definition und Datentyp.
- Codebook mit Codierungen und Missing-Regeln.
- Datenquellenverzeichnis.
- Validierungsregeln.
- Kennzeichnung von Dataset-Versionen oder Frozen States.
- Query- und Korrekturliste.

Reproduzierbare Workflows betonen, dass saubere Datenstände und dokumentierte Änderungen Voraussetzung für vertrauenswürdige Ergebnisse sind.[cite:22]

### 9.10 Analyse- und Statistikmodul

Feldlogik:[cite:22][cite:25]

- Analyseplan.
- bezogener Datensatz-Freeze.
- verantwortliche Person.
- Methode.
- Ergebnisartefakte.
- offene Fragen.
- finales Statistikmemo.

Geschäftsregel:

- keine finale Analyse ohne dokumentierten Frozen State.[cite:22]

### 9.11 Publikationsmodul

Publikationsobjekte:[cite:16][cite:25]

- Zieljournal.
- Alternativjournale.
- Autorenreihenfolge.
- Abschnittsstatus.
- Einreichungsdaten.
- Reviewer-Kommentare.
- Revisionsrunden.
- Rebuttals.
- Entscheidungen.

Statuswerte:[cite:25]

- Draft.
- Internes Review.
- Submitted.
- Minor Revision.
- Major Revision.
- Resubmitted.
- Accepted.
- Rejected.
- Published.

## 10. Geschäftsregeln

Ein Forschungsmanagement-Tool braucht verbindliche Geschäftsregeln.[cite:25][cite:30]

Wichtige Regeln:

- Keine Aktivierung von Phasen ohne erfüllte Entry-Kriterien.[cite:25]
- Kein Abschluss von Phasen ohne erfüllte Exit-Kriterien und Deliverables.[cite:25]
- Keine Datenerhebung ohne regulatorische Freigabe bei freigabepflichtigen Projekten.[cite:16][cite:30]
- Keine finale Analyse ohne Datensatz-Freeze.[cite:22]
- Kritische Änderungen am Protokoll erzeugen Change Requests.[cite:25]
- Projekte ohne Statusupdate innerhalb einer definierten Frist werden eskaliert.[cite:25]
- Risiken erhalten feste Review-Termine und Gegenmaßnahmen.[cite:25]

## 11. Dashboards und Reporting

Monitoring und Reporting sind zentrale Elemente der Projektsteuerung.[cite:25][cite:30]

Empfohlene Dashboards:

- Portfolio-Cockpit mit allen Projekten.[cite:25]
- Projekt-Timeline.
- Phase-Aging-Ansicht.
- Deadlines in 30/60/90 Tagen.
- Blocker-Dashboard.[cite:25]
- Publikationspipeline.[cite:16][cite:25]
- Review- und Freigabe-Ansichten.[cite:25]

Kennzahlen:[cite:16][cite:25]

- Zeit in aktueller Phase.
- Plan-Ist-Abweichung.
- Anzahl offener Deliverables.
- Anteil erledigter Tasks.
- Anzahl aktiver Blocker.
- Datenerhebungsfortschritt.
- Schreibfortschritt.
- Submission-Status.

## 12. Automationen

Automationen erhöhen den operativen Wert des Tools deutlich.[cite:25][cite:30]

Beispiele:

- Automatische Anlage von Standardphasen bei Projektstart.[cite:25]
- Erstellung typischer Deliverables je Projekttyp.[cite:25]
- Reminder vor Meilensteinen.
- Eskalation bei überfälligen Deliverables.[cite:25]
- Statuswechsel nur bei erfüllten Pflichtregeln.[cite:25]
- Trigger für Change Requests bei kritischen Feldänderungen.[cite:25]
- Audit-Log bei allen relevanten Status- und Feldänderungen.[cite:22][cite:25]

## 13. Nicht-funktionale Anforderungen

Neben der Fachlogik benötigt das Tool klare Qualitätsanforderungen.[cite:22][cite:25]

Pflichtpunkte:

- Rollen- und Rechtekonzept.[cite:25]
- Validierungsregeln für Pflichtfelder.[cite:25]
- vollständige Änderungsprotokollierung.[cite:22][cite:25]
- Such- und Filterfunktionen über zentrale Objekte.[cite:25]
- Export in Standardformate.[cite:25]
- sichere Ablage sensibler Informationen.[cite:25][cite:30]
- verlässliche Status- und Fristenlogik.[cite:25]

## 14. MVP und Umsetzungsreihenfolge

### 14.1 MVP-Umfang

Für eine erste Version sollte der Fokus auf Kernfunktionalität liegen.[cite:25]

Empfohlener MVP:

- Projektstammdaten.
- Phasen-Engine.
- Deliverables.
- Tasks.
- Dokumentenmodul.
- Risiko- und Blocker-Modul.
- Basis-Dashboard.
- Publikationsstatus.[cite:16][cite:25]

Später ausbaubar:[cite:25][cite:22]

- detailliertes Variablen- und Datenmanagement.
- Schnittstellen zu Literaturverwaltung oder klinischen Datenquellen.
- erweiterte Analysepakete.
- Projekttemplates nach Fachgebiet oder Studiendesign.

### 14.2 Empfohlene Bau-Reihenfolge

1. Standardprozess mit Phasen und Exit-Kriterien definieren.[cite:25]
2. Datenmodell und Beziehungen entwerfen.[cite:25]
3. Rollen- und Rechtekonzept festlegen.[cite:25]
4. Projektstammdaten und Phasen-Engine implementieren.[cite:25]
5. Deliverables und Tasks ergänzen.[cite:25]
6. Dokumenten- und Audit-Modul integrieren.[cite:22][cite:25]
7. Regulatorik- und Freigabelogik hinzufügen.[cite:16][cite:30]
8. Risiko- und Blocker-Management ergänzen.[cite:25]
9. Analyse- und Publikationsmodule aufbauen.[cite:16][cite:22][cite:25]
10. Dashboards und Automationen verfeinern.[cite:25][cite:30]

## 15. Fazit

Ein spezialisiertes Forschungsprojekt-Management-Tool ist dann besonders wertvoll, wenn mehrere Projekte parallel laufen, klinische oder regulatorische Abhängigkeiten bestehen und Veröffentlichungen aktiv gesteuert werden sollen.[cite:16][cite:25][cite:30]

Die Literatur zu Projektmanagement für Forschende und zu klinischen Projektlaufzeiten unterstützt den Ansatz, Forschung nicht als simple Task-Sammlung, sondern als mehrphasigen, freigabepflichtigen und dokumentationsintensiven Prozess zu modellieren.[cite:16][cite:22][cite:25] Die Kombination aus Phasen-Engine, Deliverables, Freigaben, Auditierbarkeit, Datenmanagement und Portfolio-Steuerung bildet den Kern eines Tools, das wissenschaftliche Arbeit wirklich adäquat abbildet.[cite:22][cite:25]
