# Umsetzungsplan: Forschungsprojekt-Management-Tool in FileMaker

Grundlage: [`00-kompendium-fachspezifikation.md`](00-kompendium-fachspezifikation.md)

Dieses Dokument beschreibt **Konzept und Bau-Reihenfolge**. Es ist bewusst kein
vollständiges Feldverzeichnis — die Detailspezifikation entsteht etappenweise
direkt vor der jeweiligen Umsetzung.

---

## 1. Leitgedanke

**Das System begleitet, es kontrolliert nicht.**

Zielgruppe sind unerfahrene Forschende *und* unerfahrene Forschungsleitende. Beide
brauchen keinen Wächter, der ihnen Knöpfe sperrt, sondern etwas, das ihnen zeigt,
wie Forschung üblicherweise abläuft und woran man an ihrer Stelle als Nächstes
denken würde.

Daraus folgt die durchgehende Gestaltungsregel:

> Das System **weiß**, wie ein Forschungsprojekt typischerweise läuft, und **sagt
> es**. Entscheiden tun Menschen.

Konkret heißt das:

- **Nichts blockiert.** Es gibt keine gesperrten Knöpfe, keine erzwungene
  Reihenfolge, keine Phase, die sich nicht abschließen lässt.
- **Alles lässt sich überspringen.** Phasen dürfen parallel laufen, ausfallen
  oder in anderer Reihenfolge stattfinden.
- **Hinweise statt Fehlermeldungen.** Wo etwas fehlt oder ungewöhnlich aussieht,
  steht ein ruhiger Satz, kein rotes Verbotsschild.
- **Jeder Hinweis lässt sich wegklicken** — mit „passt so bei uns", und dann
  kommt er nicht wieder. Ein Ratgeber, der sich nicht abstellen lässt, wird
  ignoriert oder umgangen.
- **Ein Projekt anzulegen kostet einen Titel.** Alles Weitere ist ergänzbar,
  nichts ist Voraussetzung.

Das Kompendium beschreibt in §9 und §10 ein streng regelgeführtes System mit
Pflichtfeldern, Exit-Kriterien als Sperren und erzwungenen Freigaben. Dieser Plan
übernimmt dessen **Fachwissen vollständig** — welche Phasen es gibt, was zu jeder
gehört, wo die typischen Stolpersteine liegen — verwendet es aber als **Inhalt,
nicht als Schranke**.

---

## 2. Die drei Schichten

Das ganze System besteht aus drei Schichten, die klar auseinandergehalten werden.

### Schicht 1 — Ablage

Was gibt es, wo steht es. Projekte, Phasen, Ergebnisse, Aufgaben, Dokumente,
Personen, Publikationsstand. Vollständig frei bearbeitbar.

### Schicht 2 — Orientierung

Was gehört üblicherweise dazu. Zu jeder Phase eine Checkliste mit Erklärung: Was
ist der Sinn dieser Phase, was sollte am Ende vorliegen, was ist ein typischer
Fehler, wie lange dauert das erfahrungsgemäß.

Diese Schicht ist der eigentliche Wert für Unerfahrene. Sie ist Lernmaterial, das
zum richtigen Zeitpunkt erscheint — nicht als Handbuch, das niemand liest, sondern
als das, was gerade auf dem Bildschirm relevant ist.

### Schicht 3 — Aufmerksamkeit

Worauf sollte ich gerade schauen. Weiche Hinweise, für beide Zielgruppen getrennt
formuliert:

*Für Forschende:* „Die Datenerhebung läuft seit acht Monaten. Üblich sind etwa
sechs. Gibt es etwas, das hakt?"

*Für Forschungsleitende:* „Diese drei Projekte hast du seit sechs Wochen nicht
geöffnet." — „Bei Projekt X steht die Ethikeinreichung an. Erfahrungsgemäß lohnt
sich vorher ein gemeinsamer Blick auf das Protokoll."

Schicht 3 ersetzt die Geschäftsregeln aus §10 des Kompendiums. Gleicher Inhalt,
andere Wirkung: aus „darf nicht" wird „schau mal".

---

## 3. Wie aus Regeln Hinweise werden

Jede Regel aus Kompendium §10 bleibt fachlich erhalten und wird zur Beobachtung.

| Ursprüngliche Regel | Umsetzung als Hinweis |
|---|---|
| Keine Phasenaktivierung ohne Entry-Kriterien | Beim Start einer Phase erscheint einmalig, was üblicherweise vorher vorliegt. Startet trotzdem. |
| Kein Phasenabschluss ohne Exit-Kriterien | Beim Abschließen: „Zwei Punkte der Checkliste sind offen — trotzdem abschließen?" Ja ist immer möglich. |
| Keine Datenerhebung ohne Ethikvotum | Notiz auf der Projektakte: „Ethikvotum ist nicht hinterlegt." Sichtbar, aber ohne Sperre. Wegklickbar, wenn nicht ethikpflichtig. |
| Keine finale Analyse ohne Datensatz-Freeze | Hinweis mit kurzer Erklärung, warum ein fester Datenstand vor der Auswertung sinnvoll ist. |
| Kritische Änderungen erzeugen Change Requests | Entfällt im MVP. Änderungen am Protokoll landen in der Dokumenthistorie. |
| Eskalation bei ausbleibendem Statusupdate | Kein Alarm an Vorgesetzte, sondern eine ruhige Sammelmail: „Diese Projekte melden sich länger nicht." |
| Risiken mit festem Review-Termin | Optionales Datum. Wer keins setzt, wird nicht gemahnt. |

Der Unterschied ist nicht kosmetisch. Ein gesperrter Knopf erzeugt bei
Unerfahrenen Ratlosigkeit und Ausweichverhalten — man legt das Projekt eben
außerhalb des Systems weiter. Ein erklärender Hinweis erzeugt Wissen.

---

## 4. Rahmen der ersten Ausbaustufe

| Entscheidung | Festlegung |
|---|---|
| Plattform | FileMaker Pro Clients + **FileMaker Server** (Mehrbenutzer) |
| Mindestversion | FileMaker 2023 (v20) oder neuer |
| Datenumfang | **Nur Steuerungs- und Metadaten.** Keine personenbezogenen Studiendaten |
| Funktionsumfang | MVP nach Kompendium §14.1, ergänzt um die Orientierungsschicht |
| Vorgehen | Etappenweise, jede Etappe für sich nutzbar |

**Im MVP:** Projektakte · Phasen mit Checklisten und Erklärtexten · Ergebnisse ·
Aufgaben · Dokumente mit Versionshistorie · Risiken und Blocker · Übersichtsseite
· Publikationsstand · Hinweis-Schicht · Verlaufsprotokoll.

**Später:** Variablenregister und Codebook (§9.9) · Analysepakete (§9.10) ·
vollständige Ethik-Historie · Change Requests · Lessons Learned · Schnittstellen.

---

## 5. Technische Grundsatzentscheidungen

Diese Punkte sind unabhängig von der Strenge des Systems und nachträglich teuer
zu ändern.

### 5.1 Eine Datei

Ohne Patientendaten gibt es keinen Grund zur Dateitrennung. Eine `.fmp12`-Datei
hält Schema, Daten und Oberfläche.

### 5.2 Anchor-Buoy im Beziehungsdiagramm

Pro Kontext ein eigener Anker mit den daran hängenden Tabellenauftreten. Der Graph
wird groß, aber lesbar und wartbar.

### 5.3 UUID als Primärschlüssel

Alle Schlüssel als `Get(UUID)`, nicht änderbar. Macht Vorlagenimport, spätere
Dateizusammenführung und Duplizierung konfliktfrei. Zusätzlich ein lesbarer
Projektcode (`2026-014`) für Menschen.

### 5.4 Namenskonvention

```
__pkProjectID      Primärschlüssel
_fkProjectID       Fremdschlüssel
Status             normales Feld
c_PhaseAging       Berechnungsfeld (nicht gespeichert)
s_PhaseAging       per Script gesetzter, gespeicherter Wert
g_CurrentProjectID globales Feld
zz_Utility         Hilfstabelle
```

Ohne Ausnahmen durchgehalten.

### 5.5 Checklistenpunkte sind Datensätze, keine Textfelder

Jeder Punkt einer Phasen-Checkliste ist ein eigener Datensatz mit Erklärtext,
Erledigt-Kennzeichen und Notizfeld.

Das bleibt auch im lockeren System die richtige Struktur — aus einem anderen
Grund als vorher. Nicht, damit ein Script Sperren berechnen kann, sondern damit
die **Erklärung am einzelnen Punkt hängt** und der Fortschritt sichtbar wird. Ein
Fließtextfeld kann weder erklären noch anzeigen, wie weit man ist.

### 5.6 Kennzahlen vorberechnen

Nicht gespeicherte Berechnungen sind in Listen langsam und weder sortier- noch
durchsuchbar. Deshalb zweigleisig: in der Detailansicht live gerechnet, für
Übersicht und Listen nächtlich in gespeicherte `s_`-Felder geschrieben.

### 5.7 Verlaufsprotokoll statt Audit-Log

Statt eines lückenlosen Feld-für-Feld-Protokolls ein **lesbarer Verlauf** je
Projekt: „14.03. — Phase Datenerhebung gestartet (M. Weber)", „02.04. — Protokoll
v3 hochgeladen".

Für ein begleitendes System ist das die passende Form. Es dient dem Verstehen
(„was ist hier eigentlich passiert?") und ist für neue Beteiligte und für die
Betreuung nützlich — nicht der Kontrolle. Wird per Script an den relevanten
Stellen geschrieben, nicht durch flächendeckende Überwachung.

Wo später echte Nachweispflicht entsteht (Ethik, Publikation), lässt sich für
diese Objekte gezielt ein strengeres Protokoll ergänzen.

### 5.8 Entwickeln mit dem Data Migration Tool

Ab dem ersten Echtdatensatz nicht mehr an der Live-Datei schrauben:
Entwicklungskopie ändern, `FMDataMigration` überträgt die Produktivdaten, Tausch
im Wartungsfenster.

---

## 6. Datenmodell des MVP

### 6.1 Tabellen

**Stammdaten**
- `Projects` — Projektakte. Ein Pflichtfeld: Titel. Alles andere optional.
- `ProjectTypes` — Vorhabentyp, wählt die passende Phasenvorlage vor
- `People`, `Roles`, `ProjectPeople` — wer ist mit welcher Rolle beteiligt

**Ablauf**
- `PhaseTemplates` — Katalog der üblichen Phasen je Vorhabentyp, mit Erklärtexten
- `Phases` — Phasen des konkreten Projekts, frei anleg- und löschbar
- `ChecklistItems` — Checklistenpunkte mit Erklärung, Vorlage und Instanz

**Arbeitsebene**
- `Deliverables` — Ergebnisse, die am Ende einer Phase vorliegen sollten
- `Tasks` — einzelne Aufgaben

**Nachweise und Steuerung**
- `Documents` + `DocumentVersions`
- `Risks` — was gerade hakt
- `Manuscripts` + `Submissions` — Publikationsstand
- `Nudges` — erzeugte Hinweise inkl. „weggeklickt"-Kennzeichen
- `ActivityLog` — Verlaufsprotokoll
- `zz_Utility` — globale Felder und Einstellungen

### 6.2 Kernbeziehungen

```
ProjectTypes ──< Projects ──< Phases ──< Deliverables ──< Tasks
                    │            └──< ChecklistItems
                    ├──< ProjectPeople >── People >── Roles
                    ├──< Documents ──< DocumentVersions
                    ├──< Risks
                    ├──< Manuscripts ──< Submissions
                    ├──< Nudges
                    └──< ActivityLog
```

Aufgaben hängen an einer Phase oder direkt am Projekt — beides erlaubt. Wer ohne
Phasenstruktur arbeiten will, kann das.

### 6.3 Vorlagen

Beim Anlegen eines Projekts schlägt das System anhand des Vorhabentyps die
üblichen Phasen samt Checklisten vor. Der Vorschlag erscheint zur **Bestätigung**,
nicht als Automatik: Phasen lassen sich vorher abwählen, später ergänzen oder
löschen.

Wer den Vorschlag ganz ablehnt, bekommt ein leeres Projekt. Auch das ist ein
gültiger Zustand.

---

## 7. Die Wissensschicht

Das ist der Teil, der das System für Unerfahrene wertvoll macht — und der Teil,
der die meiste inhaltliche Arbeit kostet.

Zu jeder der zwölf Phasen aus §6.1 des Kompendiums gehört hinterlegt:

- **Wozu diese Phase da ist** — zwei, drei Sätze
- **Was am Ende vorliegen sollte** — die Checkliste, jeder Punkt einzeln erklärt
- **Typische Stolpersteine** — was hier erfahrungsgemäß schiefgeht
- **Übliche Dauer** — als Orientierung, nicht als Vorgabe. Die Zahlen aus §1 des
  Kompendiums (2 Monate Projektentwicklung, 6 Monate Datenerhebung, 2 Monate
  Schreiben, 4 Monate Publikation) sind der Ausgangspunkt
- **Betreuungshinweis** — worauf eine Forschungsleitung an dieser Stelle schauen
  sollte, und was sich lohnt, im nächsten Gespräch anzusprechen

Diese Inhalte liegen als Daten in `PhaseTemplates` und `ChecklistItems`, nicht im
Programmcode. Sie sind damit ohne Entwicklungsaufwand pflegbar und wachsen mit der
eigenen Erfahrung: Was sich als wiederkehrender Stolperstein herausstellt, wird
ergänzt und hilft beim nächsten Projekt.

---

## 8. Rollen und Rechte

Bewusst schlank. Ein begleitendes System, das Leute aussperrt, widerspricht sich
selbst.

| Set | Umfang |
|---|---|
| Administration | Vollzugriff inkl. Schema und Pflege der Wissensschicht |
| Standard | Alles sehen, eigene und zugewiesene Projekte bearbeiten |
| Gast | Lesen und Export |

Die Rollen aus §8.1 des Kompendiums (PI, Projektführung, Datenerhebung, Statistik,
Regulatory, Co-Autor, Koordination) bleiben als **fachliche Zuordnung** in
`ProjectPeople` erhalten. Sie beantworten „wer kümmert sich", nicht „wer darf
klicken", und steuern, wer welchen Hinweis bekommt.

Datensatzbezogene Zugriffsbeschränkungen bleiben vorerst außen vor: sie kosten
Performance, erschweren Auswertungen und lösen ein Problem, das hier nicht besteht.

---

## 9. Oberfläche

- **Übersichtsseite** — alle Projekte mit Titel, Zuständigen, aktueller Phase,
  Stimmungsbild, nächstem Termin. Statt einer Ampel mit Verbotscharakter eine
  ruhige Kennzeichnung: läuft · stockt · ruht · abgeschlossen.
- **Projektakte** — Kopfbereich, darunter Reiter für Phasen, Ergebnisse,
  Aufgaben, Dokumente, Risiken, Publikation, Verlauf. Rechts eine schmale Spalte
  „Hinweise" mit den offenen Punkten aus Schicht 3, jeder einzeln wegklickbar.
- **Phasenansicht** — links die Checkliste mit Erklärtexten, rechts Ergebnisse und
  Aufgaben. Der Erklärtext steht sichtbar da, nicht hinter einem Fragezeichen.
- **Arbeitslisten** — „Meine Aufgaben", „Was steht an", „Was stockt".
- **Betreuungsansicht** — eigene Seite für Forschungsleitende: welche Projekte
  länger nichts gemeldet haben, wo eine Phase ungewöhnlich lange läuft, was sich
  fürs nächste Gespräch lohnt.

Grundton der Texte: sachlich und knapp, ohne Ausrufezeichen. „Läuft seit acht
Monaten" statt „ÜBERFÄLLIG!".

---

## 10. Automationen

Ein nächtlicher Serverlauf:

1. Kennzahlen neu berechnen
2. Hinweise erzeugen und veraltete zurückziehen
3. Verlaufsprotokoll verdichten

Dazu ein **wöchentlicher Sammelversand** — eine ruhige Mail pro Person mit dem,
was ansteht, statt Einzelbenachrichtigungen bei jedem Ereignis. Abschaltbar.

Rechenintensives (Projektanlage aus Vorlage, Übersichtsaufbau) läuft über
*Perform Script on Server*.

---

## 11. Bau-Reihenfolge

Jede Etappe endet mit einem nutzbaren Stand.

### Etappe 0 — Fundament
Datei, Namenskonvention, Hilfstabelle, Konten und drei Rechtesets, Serverablage,
Sicherungsplan.
*Ergebnis:* Datei liegt auf dem Server, Anmeldung funktioniert.

### Etappe 1 — Projekte und Personen
`Projects`, `ProjectTypes`, `People`, `Roles`, `ProjectPeople`, `ActivityLog`.
Projektakte mit Kopfbereich und Beteiligten. Schnellanlage: Titel eingeben, fertig.
*Ergebnis:* Projekte lassen sich anlegen und Beteiligte zuordnen.

### Etappe 2 — Phasen
`PhaseTemplates`, `Phases`, `ChecklistItems`. Die zwölf Standardphasen als
Vorlage. Vorschlagsdialog bei Projektanlage, frei anpassbar.
*Ergebnis:* Ein neues Projekt bekommt auf Wunsch seine Phasen; sie lassen sich
umsortieren, ergänzen, löschen.

### Etappe 3 — Wissensschicht *(inhaltliches Kernstück)*
Erklärtexte, Checklistenpunkte, Stolpersteine, Dauerwerte und Betreuungshinweise
für alle zwölf Phasen einpflegen. Anzeige in der Phasenansicht.
*Ergebnis:* Wer eine Phase öffnet, sieht ohne Nachfragen, worum es geht und was
dazugehört. Diese Etappe ist überwiegend Schreibarbeit, keine Entwicklung.

### Etappe 4 — Ergebnisse und Aufgaben
`Deliverables`, `Tasks`, Arbeitsliste „Meine Aufgaben".
*Ergebnis:* Aufgaben sind zuweisbar, Fortschritt wird sichtbar.

### Etappe 5 — Dokumente
`Documents` + `DocumentVersions`, Ablage mit External Secure Storage, Historie.
*Ergebnis:* Eine neue Version ergänzt die alte, statt sie zu ersetzen.

### Etappe 6 — Risiken und Blocker
`Risks`, schlank gehalten: was hakt, wer kümmert sich, seit wann.
*Ergebnis:* Stockende Projekte sind auf der Übersicht erkennbar.

### Etappe 7 — Hinweis-Schicht
`Nudges`, Regeln aus Kapitel 3, Wegklick-Mechanik, Hinweisspalte in der
Projektakte.
*Ergebnis:* Das System meldet sich von selbst — und lässt sich beruhigen.

### Etappe 8 — Publikationsstand
`Manuscripts`, `Submissions` mit den Statuswerten aus §9.11.
*Ergebnis:* Der Weg von Draft bis Published ist abbildbar.

### Etappe 9 — Übersicht, Betreuungsansicht, Serverlauf
Übersichtsseite, Betreuungsansicht, Arbeitslisten, Nachtlauf, Wochenmail.
*Ergebnis:* Die Übersicht stimmt mit den Detaildaten überein, der Nachtlauf läuft
ohne Eingriff.

### Etappe 10 — Feinschliff und Einführung
Texte überarbeiten, Export nach Excel und PDF, Testlauf mit zwei echten
Projekten, kurze Anleitung für Anwender.
*Ergebnis:* produktiv nutzbar.

Die Reihenfolge ist bewusst so gewählt, dass nach Etappe 3 bereits der
inhaltliche Kernnutzen steht — Orientierung für Unerfahrene — auch wenn Aufgaben,
Dokumente und Übersicht noch fehlen.

---

## 12. Arbeitsweise

`.fmp12`-Dateien sind Binärformat und entstehen nur in FileMaker Pro selbst.
Deshalb:

**Im Repository entsteht pro Etappe:** Detailspezifikation mit Feldliste und
Typen, Berechnungsformeln zum Kopieren, Script-Schritte in ausführbarer
Reihenfolge, Layout-Aufbau, Import-fertige CSV-Dateien für Vorlagen, Wertelisten
und die Wissensschicht.

**In FileMaker Pro passiert:** der Nachbau anhand dieser Anleitung.

So bleibt jede Etappe reproduzierbar und dokumentiert, auch wenn später jemand
anderes daran arbeitet.

---

## 13. Offene Punkte

**Vor Etappe 2:**
1. Welche Vorhabentypen sollen hinterlegt sein — retrospektive Auswertung,
   prospektive Studie, Fallserie, Review, Qualifikationsarbeit?
2. Gelten die zwölf Phasen für alle Typen gleich, oder braucht ein Review eine
   kürzere Kette?

**Vor Etappe 3 (Wissensschicht):**
3. Wer schreibt die Erklärtexte und Stolpersteine? Ich kann Entwürfe je Phase
   vorlegen, die fachliche Prüfung und der eigene Erfahrungsschatz müssen von
   Ihnen kommen — das ist der Teil, der das System vom Lehrbuch unterscheidet.
4. Sollen die Dauerwerte aus dem Kompendium (2/6/2/4 Monate) als Ausgangswerte
   dienen, oder gibt es eigene Erfahrungswerte?

**Vor Etappe 7 und 9:**
5. Ab wann gilt ein Projekt als „meldet sich länger nicht" — vier Wochen, acht?
6. Steht ein SMTP-Zugang für die Wochenmail bereit?
7. Dokumente in FileMaker-Containern oder nur Verweise auf einen bestehenden
   Netzwerkspeicher?

**Technisch:**
8. Welche FileMaker-Serverversion steht bereit, wie viele gleichzeitige Nutzer?
