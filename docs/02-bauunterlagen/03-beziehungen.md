# Beziehungsdiagramm und Bezugsformeln

**26 Beziehungen, 33 Tabellenauftreten, 7 Anker.** Alle Bezeichner deutsch.

## Grundsatz: filtern im Portal, nicht im Diagramm

Ein Portal, das nur offene Hinweise der eigenen Stufe zeigen soll, lässt sich auf
zwei Wegen bauen: über ein zusätzliches gefiltertes Tabellenauftreten, oder über
*Portal einrichten ▸ Portaldatensätze filtern*.

**Hier immer der zweite Weg.** Er spart pro Filter ein Tabellenauftreten samt
Hilfsfeldern, hält das Diagramm lesbar und ist schneller gebaut. Die
Filterformeln stehen in [`04-formeln.md`](04-formeln.md) unter F-20.

Eigene Tabellenauftreten gibt es nur dort, wo FileMaker sie zwingend braucht:
für `Summe()`-Berechnungen und für die Navigation in Skripten.

## Anordnung im Diagramm

Sieben Anker nebeneinander, die zugehörigen Auftreten jeweils darunter
(Anchor-Buoy). Kein Auftreten wird über Ankergrenzen hinweg wiederverwendet, auch
wenn es technisch ginge. Der Graph wird dadurch breiter, aber man sieht jedem
Layout an, aus welchem Kontext es kommt.

```
PROJEKT         ABSCHNITT     SCHRITT   DOKUMENT      MANUSKRIPT      VORLAGE           PERSON
Projekte        Abschnitte    Schritte  Dokumente     Manuskripte     VorlageAbschnitte Personen
 ├Abschnitte     ├Schritte     └Punkte   └Versionen    └Einreichungen  ├Schritte         └Aufgaben
 ├Schritte       ├Punkte                                               └Punkte
 ├Punkte         ├Ergebnisse
 ├Ergebnisse     ├Aufgaben
 ├Aufgaben       ├Projekte
 ├Dokumente      └Einstellungen
 ├Risiken
 ├Manuskripte
 ├Hinweise
 ├Verlauf
 ├Beteiligte
 │ ├Personen
 │ └Rollen
 └Einstellungen
```

---

## Anker 1 — PROJEKT

Kontext der Layouts `01 Mein Projekt`, `02 Meine Projekte`, `03 Projektakte`,
`05 Zeitachse`, `06 Projektauswahl`.

| Nr | Auftreten | Bezugsformel | Optionen |
|---|---|---|---|
| B-01 | `Projekte` | *Anker* | |
| B-02 | `Projekte_Abschnitte` | `Projekte::__pkProjektID = Abschnitte::_fkProjektID` | Erstellen ☑ Löschen ☑ · sortiert nach `Nr` |
| B-03 | `Projekte_Schritte` | `Projekte::__pkProjektID = Schritte::_fkProjektID` | Erstellen ☑ Löschen ☑ · sortiert nach `Nr` |
| B-04 | `Projekte_Punkte` | `Projekte::__pkProjektID = Punkte::_fkProjektID` | Erstellen ☑ Löschen ☑ · sortiert nach `SchrittNr`, `Nr` |
| B-05 | `Projekte_Ergebnisse` | `Projekte::__pkProjektID = Ergebnisse::_fkProjektID` | Erstellen ☑ Löschen ☑ |
| B-06 | `Projekte_Aufgaben` | `Projekte::__pkProjektID = Aufgaben::_fkProjektID` | Erstellen ☑ Löschen ☑ · sortiert nach `Termin` |
| B-07 | `Projekte_Dokumente` | `Projekte::__pkProjektID = Dokumente::_fkProjektID` | Erstellen ☑ Löschen ☑ |
| B-08 | `Projekte_Risiken` | `Projekte::__pkProjektID = Risiken::_fkProjektID` | Erstellen ☑ Löschen ☑ |
| B-09 | `Projekte_Hinweise` | `Projekte::__pkProjektID = Hinweise::_fkProjektID` | Erstellen ☑ Löschen ☐ · sortiert nach `Rang` |
| B-10 | `Projekte_Verlauf` | `Projekte::__pkProjektID = Verlauf::_fkProjektID` | Erstellen ☑ Löschen ☐ · sortiert nach `Zeitstempel` **absteigend** |
| B-11 | `Projekte_Manuskripte` | `Projekte::__pkProjektID = Manuskripte::_fkProjektID` | Erstellen ☑ Löschen ☑ |
| B-12 | `Projekte_Beteiligte` | `Projekte::__pkProjektID = Projektbeteiligte::_fkProjektID` | Erstellen ☑ Löschen ☑ |
| B-13 | `Projekte_Beteiligte_Personen` | `Projektbeteiligte::_fkPersonID = Personen::__pkPersonID` | |
| B-14 | `Projekte_Beteiligte_Rollen` | `Projektbeteiligte::_fkRolleID = Rollen::__pkRolleID` | |
| B-15 | `Projekte_Einstellungen` | `Projekte × zz_Einstellungen` — **Operator ×** (kartesisch) | liefert immer den einen Einstellungsdatensatz |

> **B-03 und B-04 sind der Grund für die redundanten Fremdschlüssel** in
> `Schritte` und `Punkte`. Ohne sie bräuchte Bildschirm 01 eine dreistufige
> Bezugskette. Dieselben Felder tragen zugleich die Zugriffstrennung — ein Feld,
> zwei Zwecke.

> **Zu B-15:** Der Operator `×` verbindet jeden Datensatz mit jedem. Da
> `zz_Einstellungen` genau einen Datensatz hat, ist das der einfachste Weg an die
> Einstellungen. Im Beziehungsdiagramm den Operator im Aufklappmenü zwischen den
> beiden Feldern auf `×` stellen.

---

## Anker 2 — ABSCHNITT

Kontext von Layout `04 Abschnitt`.

| Nr | Auftreten | Bezugsformel | Optionen |
|---|---|---|---|
| B-16 | `Abschnitte` | *Anker* | |
| B-17 | `Abschnitte_Schritte` | `Abschnitte::__pkAbschnittID = Schritte::_fkAbschnittID` | Erstellen ☑ Löschen ☑ · sortiert nach `Nr` |
| B-18 | `Abschnitte_Punkte` | `Abschnitte::__pkAbschnittID = Punkte::_fkAbschnittID` | Erstellen ☑ Löschen ☑ · sortiert nach `SchrittNr`, `Nr` |
| B-19 | `Abschnitte_Ergebnisse` | `Abschnitte::__pkAbschnittID = Ergebnisse::_fkAbschnittID` | Erstellen ☑ Löschen ☑ |
| B-20 | `Abschnitte_Aufgaben` | `Abschnitte::__pkAbschnittID = Aufgaben::_fkAbschnittID` | Erstellen ☑ Löschen ☑ |
| B-21 | `Abschnitte_Projekte` | `Abschnitte::_fkProjektID = Projekte::__pkProjektID` | Rückweg zum Projekt |
| B-22 | `Abschnitte_Einstellungen` | `Abschnitte × zz_Einstellungen` — **Operator ×** | nur für die grafische Zeitachse (F-14/F-15) |

---

## Anker 3 — SCHRITT

Nur für die Summenberechnungen F-12 und F-13.

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-23 | `Schritte` | *Anker* |
| B-24 | `Schritte_Punkte` | `Schritte::__pkSchrittID = Punkte::_fkSchrittID` · Erstellen ☑ Löschen ☑ · sortiert nach `Nr` |

## Anker 4 — DOKUMENT

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-25 | `Dokumente` | *Anker* |
| B-26 | `Dokumente_Versionen` | `Dokumente::__pkDokumentID = Dokumentversionen::_fkDokumentID` · Erstellen ☑ · sortiert nach `Version` absteigend |

## Anker 5 — MANUSKRIPT

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-27 | `Manuskripte` | *Anker* |
| B-28 | `Manuskripte_Einreichungen` | `Manuskripte::__pkManuskriptID = Einreichungen::_fkManuskriptID` · Erstellen ☑ · sortiert nach `Runde` |

---

## Anker 6 — VORLAGE

Nur von Skript S-03 benutzt, dafür aber tragend: die verschachtelte Bezugskette
macht das Anlegen eines Projekts zu einer einfachen Doppelschleife statt zu
mehreren Suchläufen.

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-29 | `VorlageAbschnitte` | *Anker*, sortiert nach `Nr` |
| B-30 | `VorlageAbschnitte_Schritte` | `VorlageAbschnitte::Nr = VorlageSchritte::AbschnittNr` · sortiert nach `Nr` |
| B-31 | `VorlageAbschnitte_Schritte_Punkte` | **zwei Bedingungen:**<br>`VorlageSchritte::AbschnittNr = VorlagePunkte::AbschnittNr`<br>`VorlageSchritte::Nr = VorlagePunkte::SchrittNr`<br>sortiert nach `Nr` |

> B-31 ist die einzige Beziehung mit zwei Bedingungen. Im Dialog auf *Hinzufügen*
> klicken, um das zweite Feldpaar zu ergänzen. Ohne die zweite Bedingung würden
> alle Punkte aller Schritte einer Abschnittsnummer erscheinen.

## Anker 7 — PERSON

| Nr | Auftreten | Bezugsformel |
|---|---|---|
| B-32 | `Personen` | *Anker* |
| B-33 | `Personen_Aufgaben` | `Personen::__pkPersonID = Aufgaben::_fkPersonID` · sortiert nach `Termin` |

---

## Was bewusst **keine** Beziehung ist

| Anforderung | Stattdessen |
|---|---|
| „Nur Hinweise meiner Stufe" | Portalfilter F-20.1 |
| „Nur offene Hinweise" | Portalfilter F-20.1 |
| „Nur meine Aufgaben" | Portalfilter F-20.2 über `g_MeinePersonID` |
| „Nur der aktuell laufende Abschnitt" | Portalfilter F-20.3 |
| „Nur Schritte, die zutreffen" | Portalfilter F-20.4 |
| „Nur überfällige Aufgaben" | Portalfilter F-20.6 |
| „Meine Projekte" (Projektliste) | Suchlauf in Skript S-02 |
| **„Nur Projekte, die ich sehen darf"** | **Zugriffsrechte im Rechteset, nicht im Diagramm** — Doc 07 |

Der letzte Punkt ist der wichtigste. Sichttrennung über Beziehungen oder Filter
wäre Kosmetik; sie gehört ins Rechtesystem, wo sie auch dann greift, wenn jemand
ein eigenes Layout baut oder exportiert.

---

## Reihenfolge beim Bauen

1. Alle 20 Tabellen importieren (Doc 08) — FileMaker legt dabei je ein
   Tabellenauftreten mit dem Tabellennamen an
2. Die sieben Anker aus diesen Auftreten heraussuchen und nebeneinander legen
3. Weitere Auftreten über *Duplizieren* erzeugen und nach obiger Liste umbenennen
4. Beziehungen ziehen, Formeln und Optionen setzen
5. Erst danach die Berechnungsfelder aus Doc 04 anlegen — sie greifen teils auf
   Bezüge zu und lassen sich vorher nicht speichern
