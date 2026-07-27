# Die Bildschirme — Ausgangspunkt der Konstruktion

Alles Weitere ist aus diesen sechs Bildschirmen abgeleitet. Jeder Wert, der hier
steht, erzeugt weiter hinten ein Feld, eine Beziehung oder eine Formel — und
umgekehrt gibt es nichts, was hier nicht gebraucht wird.

Die Spalte **⇒** nennt die Herleitung: welches Feld, welche Beziehung, welche
Formel den Wert liefert.

---

## Bildschirm 01 — „Mein Projekt" (Stufe Forschende)

Einstiegsseite nach der Anmeldung für Forschende. Ein Projekt, nah dran.

```
┌──────────────────────────────────────────────────────────────────────┐
│  Kohortenstudie Wundheilung nach Revisionseingriff          2026-014 │
│                                                                       │
│  Monat 9 von 24. Planmäßig wären Sie jetzt im Sammeln — das passt.   │
│  ▓▓▓▓▓▓▓▓░░░░░░░░░░░░░░░  Einreichung voraussichtlich März 2028      │
├────────────────────────────────────────┬─────────────────────────────┤
│                                        │  Hinweise                   │
│  Abschnitt 3 — Sammeln                 │                             │
│  Woher kommt mein Material?            │  Die Datenerhebung läuft     │
│                                        │  seit vier Monaten. Ein      │
│  ▸ Material zusammentragen             │  Zwischenblick auf die       │
│    ☑ Erhebungsbogen steht              │  Vollständigkeit lohnt sich  │
│    ☑ Erste zehn Fälle erfasst          │  jetzt mehr als am Ende.     │
│    ☐ Erfassung läuft nach Plan         │                    [ passt ] │
│         Bei Rückstand früh melden —    │  ─────────────────────────   │
│         eine Verlängerung ist einfacher│  Kein Ethikvotum hinterlegt. │
│         als eine Notlösung.            │                    [ passt ] │
│    ☐ Abweichungen dokumentiert         │                             │
│                                        │                             │
│  ▸ Laufende Qualitätskontrolle         │                             │
│    ☐ Doppelerfassung stichprobenartig  │                             │
│    ☐ Plausibilitätsprüfung eingerichtet│                             │
├────────────────────────────────────────┴─────────────────────────────┤
│  Meine offenen Aufgaben                                              │
│  ▸ Fälle Q3 nachtragen                              fällig 12.11.    │
│  ▸ Rücksprache Statistik                            fällig 20.11.    │
└──────────────────────────────────────────────────────────────────────┘
```

| Element | ⇒ Herleitung |
|---|---|
| Projekttitel | `Projects::Titel` |
| Projektcode rechts | `Projects::c_Projektcode` (Formel F-01) |
| **Standortsatz** | `Projects::c_Standortsatz` (Formel F-05) — der wichtigste Wert im System |
| Fortschrittsbalken | `Projects::c_ZeitfortschrittProzent` (F-06), als Balkendiagramm-Objekt |
| „Einreichung voraussichtlich" | `Projects::c_EinreichungVoraussichtlich` (F-08) |
| Abschnittsnummer und -name | Portal auf `Projects_Sections` (B-02), Filter F-20.3 `Status = "läuft"` |
| Leitfrage | `Sections::Leitfrage` |
| Schrittliste | Portal auf `Projects_Steps` (B-03), Filter F-20.4 |
| Checklistenpunkte | Portal auf `Projects_ChecklistItems` (B-04), Filter F-20.5, sortiert nach `StepNr, Nr` |
| Erklärtext unter dem Punkt | `ChecklistItems::Erklaerung`, sichtbar bei aktivem Datensatz (F-21) |
| Hinweisspalte | Portal auf `Projects_Nudges` (B-09), Filter F-20.1 |
| Knopf „passt" | Skript S-06 *Hinweis wegklicken* |
| Meine Aufgaben | Portal auf `Projects_Tasks` (B-06), Filter F-20.2 |

**Bei mehreren eigenen Projekten** erscheint davor eine schlichte Auswahlliste
(Layout `06 Projektauswahl`).

---

## Bildschirm 02 — „Meine Projekte" (Stufe Forschungsleitung)

Einstiegsseite nach der Anmeldung für die Forschungsleitung.

```
┌──────────────────────────────────────────────────────────────────────┐
│  Meine Projekte                                          7 Projekte  │
├──────────────────────────────────────────────────────────────────────┤
│  Projekt                     Bearbeitung   Stand      Einreichung    │
│  ─────────────────────────────────────────────────────────────────   │
│  Wundheilung Revision        M. Weber      M 9/24     März 2028      │
│                                            läuft                     │
│  Antibiotikaprophylaxe       L. Sander     M 14/24    Aug 2027       │
│                                            stockt                    │
│  Registerauswertung Hüfte    T. Brand      M 3/24     Jan 2029       │
│                                            läuft                     │
├──────────────────────────────────────────────────────────────────────┤
│  Was sich zu besprechen lohnt                                        │
│                                                                       │
│  Antibiotikaprophylaxe · L. Sander                                    │
│  Seit sieben Wochen keine Bewegung, mitten in der Erhebung. Fragen    │
│  Sie nach der Fallzahl statt nach dem Zeitplan — meist steckt dort    │
│  das Problem.                                                         │
│                                                                       │
│  Registerauswertung Hüfte · T. Brand                                  │
│  Steht vor der Ethikeinreichung. Ein gemeinsamer Blick aufs Protokoll │
│  vorher spart erfahrungsgemäß eine Rückfragenrunde.                   │
└──────────────────────────────────────────────────────────────────────┘
```

| Element | ⇒ Herleitung |
|---|---|
| Projektliste | Listenlayout auf `Projects`, Suchlauf über `ProjectPeople` (Skript S-02) |
| „M 9/24" | `Projects::s_MonatImProjekt` und `Projects::BogenLaengeMonate` — **`s_`, nicht `c_`**, sonst ist die Liste langsam und nicht sortierbar |
| Zustand | `Projects::s_Zustand` (Formel F-10, nachts geschrieben) |
| Einreichung | `Projects::s_EinreichungVoraussichtlich` |
| **„Was sich zu besprechen lohnt"** | Portal auf `Projects_Nudges` (B-09), Filter F-20.1, sortiert nach `Rang` |
| Text des Vorschlags | `Nudges::Text`, erzeugt von Skript S-05 aus der Wissensschicht |

Dieser untere Bereich ist der eigentliche Grund für die zweite Stufe. Er beantwortet
die Frage, die eine unerfahrene Betreuung nicht stellen kann: *Worauf schaue ich
bei einem Projekt in Monat 9 überhaupt?*

---

## Bildschirm 03 — Projektakte

Gemeinsam für beide Stufen. Kopfbereich plus Registerkarten.

```
┌──────────────────────────────────────────────────────────────────────┐
│  ‹ zurück      Wundheilung Revision              2026-014     läuft  │
│  Monat 9 von 24 · Sammeln · Einreichung voraussichtlich März 2028    │
├──────────────────────────────────────────────────────────────────────┤
│ │Abschnitte│ Ergebnisse │ Aufgaben │ Dokumente │ Risiken │ Publikation │ Verlauf │
├──────────────────────────────────────────────────────────────────────┤
│                                                                       │
│   1  Klären                M 1–4      abgeschlossen      12/12       │
│   2  Planen                M 4–9      abgeschlossen      14/14       │
│   3  Sammeln               M 8–16     läuft               3/9        │
│   4  Auswerten             M 15–19    offen               0/11       │
│   5  Veröffentlichen       M 18–24    offen               0/15       │
│                                                                       │
└──────────────────────────────────────────────────────────────────────┘
```

| Element | ⇒ Herleitung |
|---|---|
| Kopfzeile | wie Bildschirm 01, kompakter |
| Registerkarten | Registerkartenobjekt, je Reiter ein Portal |
| Abschnittsliste | Portal auf `Projects_Sections` (B-02), sortiert nach `Nr` |
| „M 1–4" | `Sections::Band_Von_Monat` & `Sections::Band_Bis_Monat` |
| „12/12" | `Sections::c_PunkteErledigt` / `c_PunkteGesamt` (F-12, F-13) |
| Reiter Ergebnisse | Portal `Projects_Deliverables` (B-05) |
| Reiter Aufgaben | Portal `Projects_Tasks` (B-06) |
| Reiter Dokumente | Portal `Projects_Documents` (B-07) |
| Reiter Risiken | Portal `Projects_Risks` (B-08) |
| Reiter Publikation | Portal `Projects_Manuscripts` (B-11) |
| Reiter Verlauf | Portal `Projects_ActivityLog` (B-10), absteigend nach Zeitstempel |

---

## Bildschirm 04 — Abschnittsansicht

Aus der Projektakte durch Klick auf einen Abschnitt.

```
┌──────────────────────────────────────────────────────────────────────┐
│  ‹ Projektakte     Abschnitt 3 — Sammeln           M 8–16     läuft  │
├──────────────────────────────────────────────────────────────────────┤
│  Woher kommt mein Material?                                          │
│                                                                       │
│  Wozu dieser Abschnitt da ist                                        │
│  Hier entsteht die Substanz der Arbeit. Der Abschnitt ist der         │
│  längste und der unspektakulärste; die meisten Projekte scheitern     │
│  nicht an der Analyse, sondern daran, dass die Erhebung versandet.    │
│                                                                       │
│  Was hier erfahrungsgemäß schiefgeht                                  │
│  Die Erhebung wird neben dem Tagesgeschäft „mitgemacht" und verliert  │
│  nach zwei Monaten an Tempo. Wer feste Zeitfenster einplant, kommt    │
│  durch; wer auf ruhige Wochen wartet, nicht.                          │
├────────────────────────────────────────┬─────────────────────────────┤
│  Checkliste                            │  Ergebnisse dieses Abschnitts│
│                                        │                             │
│  ▸ Material zusammentragen             │  ▸ Erhebungsbogen v2         │
│    ☑ Erhebungsbogen steht              │    freigegeben 04.09.        │
│    ☑ Erste zehn Fälle erfasst          │  ▸ Datensatz Zwischenstand   │
│    ☐ Erfassung läuft nach Plan         │    offen                     │
│    ☐ Abweichungen dokumentiert         │                             │
│                          [Notiz]       │  Aufgaben                    │
│                                        │  ▸ Fälle Q3 nachtragen       │
│  ▸ Laufende Qualitätskontrolle         │  ▸ Rücksprache Statistik     │
│    ☐ Doppelerfassung stichprobenartig  │                             │
│    ☐ Plausibilitätsprüfung eingerichtet│                             │
└────────────────────────────────────────┴─────────────────────────────┘
```

| Element | ⇒ Herleitung |
|---|---|
| „Wozu dieser Abschnitt da ist" | `Sections::Zweck` — beim Anlegen aus `SectionTemplates` kopiert |
| „Was schiefgeht" | `Sections::Stolpersteine` |
| Checkliste | **ein** Portal auf `Sections_ChecklistItems` (B-18), sortiert nach `StepNr, Nr` — Gliederung über Überschriftszeilen, siehe F-11 |
| Häkchen | `ChecklistItems::Erledigt`, Klick löst Skript S-04 aus |
| Notizknopf | `ChecklistItems::Notiz` in einem Karten-Fenster |
| Ergebnisse | Portal `Sections_Deliverables` (B-19) |

**Für die Stufe Leitung** erscheint hier zusätzlich ein Kasten *Worauf zu schauen
ist* mit `Sections::Hinweis_Leitung`. Für Forschende bleibt er ausgeblendet
(bedingte Formatierung über `g_MeineStufe`).

---

## Bildschirm 05 — Zeitachse

```
┌──────────────────────────────────────────────────────────────────────┐
│  Zeitachse                            Monat  1    6    12   18   24  │
│                                              │    │    │    │    │   │
│  1 Klären              ▓▓▓▓                                          │
│                        ████                                          │
│  2 Planen                 ▓▓▓▓▓▓                                     │
│                           ██████                                     │
│  3 Sammeln                    ▓▓▓▓▓▓▓▓▓                              │
│                               ███████                                │
│  4 Auswerten                            ▓▓▓▓▓                        │
│  5 Veröffentlichen                          ▓▓▓▓▓▓▓                  │
│                                                                       │
│                        ▓ geplant   █ tatsächlich        ▲ heute      │
└──────────────────────────────────────────────────────────────────────┘
```

| Element | ⇒ Herleitung |
|---|---|
| Soll-Balken | Rechteck, Breite aus `Sections::c_BandBreitePx` (F-14), Position aus `c_BandStartPx` |
| Ist-Balken | aus `Ist_Start` und `Ist_Ende` bzw. heute, Formel F-15 |
| Heute-Marke | senkrechte Linie, Position aus `Projects::c_HeutePx` (F-16) |

Umsetzung als Portal auf `Projects_Sections` mit einem Rechteck-Objekt je Zeile,
dessen linker Rand und Breite über *Objekt ▸ Ausblenden wenn* und die
Balkendiagramm-Darstellung gesteuert werden. Details Doc 06 §6.

Die Zeitachse ist der einzige Bildschirm, der ohne Erklärtext auskommt — sie ist
selbst die Erklärung.

---

## Bildschirm 06 — Projektauswahl

Schlichte Liste, wenn jemand mehr als ein eigenes Projekt hat. Titel, Standort,
Zustand. Klick öffnet Bildschirm 01 oder 03.

---

## Was daraus folgt

Aus diesen Bildschirmen ergeben sich:

- **20 Tabellen** — Doc 02
- **26 Bezüge** in 33 Tabellenauftreten; sieben gefilterte Ansichten kommen ohne
  eigenes Auftreten aus und werden per Portalfilter erledigt — Doc 03
- **23 Formeln** und eine Custom Function, davon sechs tragende: Standortsatz,
  Verzug, voraussichtliche Einreichung, Zustand, Fortschritt, Zeitachse — Doc 04
- **9 Skripte** — Doc 05
- **6 Arbeitslayouts, 9 Hilfslayouts** — Doc 06
