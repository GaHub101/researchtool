# Import, Server und Testlauf

## 1 · Datei anlegen

Neue Datei `Forschung.fmp12`. In *Datei ▸ Dateioptionen*:

- *Beim Öffnen* ▸ Layout `90 Projekte_Hilfslayout`
- *Beim Öffnen* ▸ Skript **S-01 Start** ausführen
- Standardkonto `Admin` ohne Kennwort entfernen, sobald die echten Konten stehen

Konten und Rechtesets: [`07-rechte-und-mehrbenutzer.md`](07-rechte-und-mehrbenutzer.md)

## 2 · Import der Startdaten

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
|---|---:|---|
| `VorlageAbschnitte.tab` | 5 | die fünf Abschnitte mit allen Erklärtexten |
| `VorlageSchritte.tab` | 13 | die dreizehn Schritte |
| `VorlagePunkte.tab` | 59 | alle Checklistenpunkte samt Erklärung |
| `Rollen.tab` | 7 | Rollenbezeichnungen |
| `Hinweistexte.tab` | 7 | die sieben Hinweisregeln in beiden Fassungen |
| 16 weitere | 0 | nur Kopfzeile — erzeugen Tabelle und Feldstruktur |

`Hinweistexte.tab` ist **keine Tabelle des laufenden Systems**, sondern eine
Vorlage: Die Texte werden beim Bau in Skript S-05 eingetragen. Als
Nachschlagetabelle importiert schadet sie nicht.

### Nach dem Import

1. **Feldtypen korrigieren** — Liste in [Doc 02 §22](02-tabellen-und-felder.md)
2. **Primärschlüssel-Automatik setzen** — bei jedem `__pk…`:
   *Automatisch eingeben ▸ Berechneter Wert* `Hole ( UUID )`, dazu
   *Feldinhalt darf nicht geändert werden*
3. **Zeitstempel-Automatik setzen** — `ErstelltAm`, `ErstelltVon`, `GeaendertAm`,
   `GeaendertVon`, `ErzeugtAm`, `HochgeladenAm`, `Zeitstempel`, `Konto`
4. **Containerfeld `Dokumentversionen::Datei` von Hand anlegen** — Import kann
   keine Container erzeugen. Speicherung ▸ *Container extern sichern*
5. **Vorgabewerte setzen** — `BogenLaengeMonate` = 24,
   `ZielpunktEinreichungMonat` = 22, `Status` = `aktiv`,
   `Ethikpflicht` = `unklar`, alle `Erledigt` / `TrifftNichtZu` /
   `IstUeberschrift` / `Weggeklickt` / `Zurueckgezogen` = 0
6. **`zz_Einstellungen` befüllen** — genau ein Datensatz:
   `StilleFristTage` 42 · `RuhtFristTage` 120 · `WochenimpulsAktiv` 1 ·
   `ZeitachseBreitePx` 600 · Absender und SMTP nach Gegebenheit
7. **Berechnungs- und globale Felder anlegen** — alle ⊕-Felder aus Doc 02,
   Formeln aus Doc 04

> Die drei Vorlagentabellen kommen **ohne Primärschlüsselfeld**. Sie brauchen
> keins: Die Beziehungen B-30 und B-31 laufen über die Nummern.

## 3 · Personen anlegen

Vor dem ersten Projekt: für jede Person ein FileMaker-Konto **und** einen
Datensatz in `Personen` mit identischem `Kontoname`. Details und Prüfung in
[Doc 07 §5](07-rechte-und-mehrbenutzer.md).

Ohne diese Verbindung sieht ein Konto der Stufe *Forschende* kein einziges
Projekt — die Zugriffsformeln finden dann keine Personen-ID zum Vergleichen.

## 4 · Serverzeitpläne

FileMaker Server ▸ *Konfiguration ▸ Skriptzeitpläne*. Beide brauchen ein Konto
mit dem Rechteset `Administration` — nur damit sehen sie alle Projekte.

**Nachtlauf** — täglich 03:00

```
1. Skript: S-08 Kennzahlen aktualisieren     Parameter: (leer)
2. Skript: S-11 Zugriffsliste aktualisieren  Parameter: (leer → alle Projekte)
```

S-08 rechnet Standort, Verzug, voraussichtliche Einreichung und Zustand neu und
ruft je Projekt S-05 auf. S-11 verteilt die Zugriffslisten neu und fängt damit
Zuordnungen ab, die von Hand geändert wurden. Laufzeit bei 50 Projekten unter
einer Minute.

**Wochenimpuls** — montags 07:00

```
Skript: S-10 Wochenimpuls
```

S-10 ist noch nicht gebaut (Etappe 10 des Umsetzungsplans) und braucht den
SMTP-Zugang.

## 5 · Sicherung und Weiterentwicklung

- Serverseitige Sicherung: täglich, sieben Stände aufbewahren
- Container extern gesichert — der Ordner gehört mit in die Sicherung
- **Nicht aktive Clients trennen nach 3 Stunden** — verhindert über Nacht offene
  Datensatzsperren (Doc 07 §6)
- **Ab dem ersten Echtdatensatz nicht mehr an der Live-Datei arbeiten.**
  Entwicklungskopie ändern, mit dem *FileMaker Data Migration Tool* die
  Produktivdaten übertragen, im Wartungsfenster tauschen

## 6 · Testlauf vor der Freigabe

Zehn fachliche Prüfungen. Die sechs Rechteprüfungen stehen in
[Doc 07 §7](07-rechte-und-mehrbenutzer.md) und gehören dazu.

| # | Prüfung | Erwartung |
|---|---|---|
| 1 | Anmeldung mit Rechteset `Forschende` | landet auf `01 Mein Projekt` oder `06` |
| 2 | Anmeldung mit `Forschungsleitung` | landet auf `02 Meine Projekte` |
| 3 | Neues Projekt anlegen (S-03) | 5 Abschnitte, 13 Schritte, 59 Punkte + 13 Überschriften |
| 4 | Startdatum auf vor 9 Monaten setzen | Standortsatz nennt **Monat 9 von 24** |
| 5 | Abschnitt 3 starten | Standortsatz sagt „das passt" |
| 6 | Abschnitt 1 statt 3 starten | Satz nennt Rückstand **ohne Vorwurf**, Einreichung rückt |
| 7 | Abschnitt mit offenen Punkten abschließen | Rückfrage erscheint, **Abschließen ist möglich** |
| 8 | Hinweis wegklicken, S-05 erneut laufen lassen | Hinweis kommt **nicht** wieder |
| 9 | Schritt auf „trifft nicht zu" | verschwindet aus der Liste, Zähler sinkt |
| 10 | Nachtlauf von Hand starten | `s_`-Felder gefüllt, Leitungsliste sortierbar |

**Prüfung 7 ist die wichtigste fachliche.** Wenn dort etwas blockiert, ist der
Leitgedanke verletzt und das System für seine Zielgruppe falsch gebaut.

**Prüfung 6 in Doc 07 ist die wichtigste technische** — der Export als Nachweis,
dass die Sichttrennung im Rechtesystem sitzt und nicht in der Oberfläche.

## 7 · Was noch fehlt

Bewusst außerhalb dieser Unterlagen, weil es zum MVP nicht gebraucht wird:

- **S-10 Wochenimpuls** — Etappe 10, braucht den SMTP-Zugang
- **Layout `02b Besprechungsliste`** — projektübergreifende Leitungsansicht,
  Doc 06 §2
- **Beteiligten-Dialog** — Oberfläche zum Zuordnen von Personen, ruft S-11 auf.
  Bis dahin: Zuordnung im Portal auf Layout `03`, danach S-11 von Hand starten
- **Export nach Excel und PDF** — Etappe 11, FileMaker-Bordmittel
- **Variablenregister, Analysepakete, Änderungsanträge** — Stufe 2 des
  Umsetzungsplans
