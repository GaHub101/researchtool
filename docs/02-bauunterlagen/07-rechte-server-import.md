# Konten, Import, Server, Testlauf

## 1 · Datei anlegen

Neue Datei `Forschung.fmp12`. In den Dateioptionen:

- *Beim Öffnen* ▸ Skript **S-01 Start** ausführen
- *Beim Öffnen* ▸ Layout `90 Projects_Utility` (die Skripte leiten weiter)
- Standardkonto `Admin` ohne Kennwort **entfernen**, sobald die echten Konten
  stehen

## 2 · Rechtesets

Nur drei — der Leitgedanke verträgt kein feingliedriges Rechtesystem.

| Rechteset | Datensätze | Layouts | Wertelisten | Skripte | Sonstiges |
|---|---|---|---|---|---|
| `Forschende` | Alle erstellen, ändern, löschen | Alle einsehbar | Alle änderbar | Alle ausführbar | Kein Zugriff auf Schema |
| `Forschungsleitung` | wie oben | wie oben | wie oben | wie oben | zusätzlich `Datei drucken`, `Exportieren` |
| `Administration` | Voll | Voll | Voll | Voll | Schema, Wertelisten, Wissensschicht |

**Erweiterte Rechte:** bei allen dreien `fmapp` (FileMaker-Netzwerkzugriff)
aktivieren; bei `Administration` zusätzlich `fmreauthenticate`.

> **Warum dürfen Forschende alle Projekte sehen?** Weil Abschottung dem Zweck
> widerspricht. Beim ersten eigenen Projekt ist es hilfreich, in ein fremdes
> hineinzuschauen. Der Unterschied zwischen den Stufen liegt in der
> Einstiegsseite und in den Texten, nicht in Verboten.

> Die Namen der Rechtesets sind fachlich bedeutsam: S-01 Zeile 7 leitet daraus
> die Stufe ab. Wer sie umbenennt, muss dort nachziehen.

## 3 · Import der Startdaten

**Reihenfolge egal**, aber alle 21 Dateien aus `seed/` müssen importiert werden —
auch die leeren. Sie erzeugen Tabelle und Felder.

```
Datei ▸ Datensätze importieren ▸ Datei…
  Dateityp:      Tabulatorgetrennte Textdateien (*.tab)
  Datei:         seed/<Name>.tab
  Ziel:          ◉ Neue Tabelle
  Zeichensatz:   Unicode (UTF-8)
  ☑ Erste Datenzeile enthält Feldnamen
```

| Datei | Zeilen | Inhalt |
|---|---|---|
| `SectionTemplates.tab` | 5 | die fünf Abschnitte mit allen Erklärtexten |
| `StepTemplates.tab` | 13 | die dreizehn Schritte |
| `ChecklistTemplates.tab` | 59 | alle Checklistenpunkte samt Erklärung |
| `Roles.tab` | 7 | Rollenbezeichnungen |
| `Nudgetexte.tab` | 7 | die sieben Hinweisregeln in beiden Fassungen |
| 16 weitere | 0 | nur Kopfzeile — erzeugen Tabelle und Felder |

`Nudgetexte.tab` ist **keine Tabelle des Systems**, sondern eine Vorlage zum
Abschreiben: Die Texte werden in Skript S-05 eingetragen. Man kann die Datei
trotzdem importieren und als Nachschlagetabelle behalten.

### Nach dem Import

1. **Feldtypen korrigieren** — Liste in Doc 02 §22
2. **Primärschlüssel-Automatik setzen** — bei jedem `__pk…`:
   *Automatisch eingeben ▸ Berechneter Wert* `Get ( UUID )`, dazu
   *Feldinhalt darf nicht geändert werden*
3. **Zeitstempel-Automatik setzen** — `ErstelltAm`, `ErstelltVon`, `GeaendertAm`,
   `GeaendertVon`, `ErzeugtAm`, `HochgeladenAm`, `Zeitstempel`, `Konto`
4. **Containerfeld `DocumentVersions::Datei` von Hand anlegen** — Import kann
   keine Container erzeugen. Speicherung ▸ *Container extern sichern*
5. **Vorgabewerte setzen** — `BogenLaengeMonate` = 24,
   `ZielpunktEinreichungMonat` = 22, `Status` = `aktiv`,
   `Ethikpflicht` = `unklar`, alle `Erledigt` / `TrifftNichtZu` /
   `IstUeberschrift` / `Weggeklickt` / `Zurueckgezogen` = 0
6. **`zz_Utility` befüllen** — genau ein Datensatz:
   `StilleFristTage` 42 · `RuhtFristTage` 120 · `WochenimpulsAktiv` 1 ·
   `ZeitachseBreitePx` 600 · Absender und SMTP nach Gegebenheit
7. **Berechnungs- und globale Felder anlegen** — alle ⊕-Felder aus Doc 02, Formeln
   aus Doc 04

> Die drei Vorlagentabellen kommen **ohne Primärschlüsselfeld**. Sie brauchen
> keins: Die Beziehungen B-30 und B-31 laufen über `Nr` / `SectionNr` / `StepNr`.
> Wer trotzdem eins möchte, legt es von Hand an.

## 4 · Serverzeitpläne

FileMaker Server ▸ *Konfiguration ▸ Skriptzeitpläne*. Beide brauchen ein Konto
mit dem Rechteset `Administration`.

**Nachtlauf** — täglich 03:00

```
Skript:   S-08 Kennzahlen aktualisieren
Parameter: (leer — dann alle Projekte)
```

Rechnet Standort, Verzug, voraussichtliche Einreichung und Zustand neu, ruft für
jedes Projekt S-05 auf und erzeugt oder zieht Hinweise zurück. Laufzeit bei 50
Projekten unter einer Minute.

**Wochenimpuls** — montags 07:00

```
Skript:   S-10 Wochenimpuls
```

S-10 ist noch nicht gebaut (Etappe 10 des Umsetzungsplans). Aufbau: Schleife über
`People` mit `Aktiv = 1`, je Person die offenen Hinweise ihrer Stufe und ihre
fälligen Aufgaben zusammenstellen, eine Mail senden. Abschaltbar über
`zz_Utility::WochenimpulsAktiv`.

Eine ruhige Mail pro Woche — keine Einzelbenachrichtigung bei jedem Ereignis.

## 5 · Sicherung und Entwicklung

- Serverseitige Sicherung: täglich, sieben Stände aufbewahren
- **Ab dem ersten Echtdatensatz nicht mehr an der Live-Datei arbeiten.**
  Entwicklungskopie ändern, mit dem *FileMaker Data Migration Tool* die
  Produktivdaten übertragen, im Wartungsfenster tauschen
- Container extern gesichert — der Ordner gehört mit in die Sicherung

## 6 · Testlauf vor der Freigabe

Zehn Prüfungen. Jede hat ein eindeutiges Ergebnis.

| # | Prüfung | Erwartung |
|---|---|---|
| 1 | Anmeldung mit `Forschende` | landet auf `01 Mein Projekt` oder `06` |
| 2 | Anmeldung mit `Forschungsleitung` | landet auf `02 Meine Projekte` |
| 3 | Neues Projekt anlegen (S-03) | 5 Abschnitte, 13 Schritte, 59 Punkte + 13 Überschriften |
| 4 | Startdatum auf vor 9 Monaten setzen | Standortsatz nennt **Monat 9 von 24** |
| 5 | Abschnitt 3 starten | Standortsatz sagt „das passt" |
| 6 | Abschnitt 1 statt 3 starten | Satz nennt Rückstand **ohne Vorwurf**, Einreichung rückt |
| 7 | Abschnitt mit offenen Punkten abschließen | Rückfrage erscheint, **Abschließen ist möglich** |
| 8 | Hinweis wegklicken, S-05 erneut laufen lassen | Hinweis kommt **nicht** wieder |
| 9 | Schritt auf „trifft nicht zu" | verschwindet aus der Liste, Zähler sinkt |
| 10 | Nachtlauf von Hand starten | `s_`-Felder gefüllt, Leitungsliste sortierbar |

**Prüfung 7 ist die wichtigste.** Wenn dort etwas blockiert, ist der Leitgedanke
verletzt und das System für seine Zielgruppe falsch gebaut.

## 7 · Was noch fehlt

Bewusst außerhalb dieser Unterlagen, weil es zum MVP nicht gebraucht wird:

- **S-10 Wochenimpuls** — Etappe 10, braucht den SMTP-Zugang
- **Layout `02b Besprechungsliste`** — projektübergreifende Leitungsansicht,
  Doc 06 §2
- **Export nach Excel und PDF** — Etappe 11, FileMaker-Bordmittel
- **Variablenregister, Analysepakete, Change Requests** — Stufe 2 des
  Umsetzungsplans
