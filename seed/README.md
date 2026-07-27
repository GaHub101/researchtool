# Startdaten für den Import

21 tabulatorgetrennte Dateien (UTF-8). Jede erzeugt beim Import in FileMaker
**Tabelle und Felder in einem Schritt** — das ist der größte Einzelposten an
gesparter Klickarbeit.

```
Datei ▸ Datensätze importieren ▸ Datei…
  Dateityp:      Tabulatorgetrennte Textdateien (*.tab)
  Ziel:          ◉ Neue Tabelle
  Zeichensatz:   Unicode (UTF-8)
  ☑ Erste Datenzeile enthält Feldnamen
```

Vollständige Anleitung: [`../docs/02-bauunterlagen/08-import-server-test.md`](../docs/02-bauunterlagen/08-import-server-test.md)

## Dateien mit Inhalt

| Datei | Zeilen | Inhalt |
|---|---:|---|
| `VorlageAbschnitte.tab` | 5 | Die fünf Abschnitte des 24-Monats-Bogens — Leitfrage, Monatsbereiche, Zweck, Stolpersteine, Hinweise für beide Stufen |
| `VorlageSchritte.tab` | 13 | Die Schritte unter den Abschnitten, ebenfalls mit beiden Textfassungen |
| `VorlagePunkte.tab` | 59 | Alle Checklistenpunkte, jeder mit Erklärung |
| `Rollen.tab` | 7 | Rollenbezeichnungen |
| `Hinweistexte.tab` | 7 | Die sieben Hinweisregeln mit Bedingung und beiden Textfassungen |

Zusammen sind das **84 redaktionelle Einträge** — die vollständige Wissensschicht.
Sie ist der Teil des Systems, der ohne Vorarbeit Wochen kostet, und der Teil, der
sich am leichtesten weiterentwickeln lässt: Alles liegt als Daten in der
Datenbank, nichts im Programm.

`Hinweistexte.tab` ist keine Tabelle des laufenden Systems, sondern eine Vorlage —
die Texte werden beim Bau in Skript S-05 eingetragen. Als Nachschlagetabelle
importiert schadet sie nicht.

## Dateien nur mit Kopfzeile

`Projekte` · `Personen` · `Projektbeteiligte` · `Abschnitte` · `Schritte` ·
`Punkte` · `Ergebnisse` · `Aufgaben` · `Dokumente` · `Dokumentversionen` ·
`Risiken` · `Manuskripte` · `Einreichungen` · `Hinweise` · `Verlauf` ·
`zz_Einstellungen`

Sie enthalten keine Datensätze, erzeugen aber Tabelle und Feldstruktur.

## Nach dem Import

Alle Felder stehen zunächst auf **Text**. Was zu korrigieren ist und welche
Felder von Hand zu ergänzen sind — Berechnungen, Container, globale Felder —
steht in [`../docs/02-bauunterlagen/02-tabellen-und-felder.md`](../docs/02-bauunterlagen/02-tabellen-und-felder.md),
Abschnitt 22.

Jede Datentabelle trägt ein Feld `s_ZugriffIDs`. Es steuert, wer den Datensatz
sehen darf, und wird von Skript S-11 gefüllt — siehe
[`../docs/02-bauunterlagen/07-rechte-und-mehrbenutzer.md`](../docs/02-bauunterlagen/07-rechte-und-mehrbenutzer.md).

## Texte ändern

Die Erklärtexte sind Entwürfe. Sie beruhen auf dem Kompendium und auf dem, was in
klinischen Forschungsprojekten typischerweise schiefgeht — nicht auf Ihrer
konkreten Umgebung.

Zwei Wege zur Anpassung:

- **Vor dem Import:** Dateien hier ändern, in einem Editor mit UTF-8 und
  Tabulator-Trennung. Keine Zeilenumbrüche innerhalb eines Feldes.
- **Nach dem Import:** direkt in FileMaker in den Vorlagentabellen. Änderungen
  wirken auf **neue** Projekte; laufende behalten ihre Texte, weil S-03 sie beim
  Anlegen kopiert.

Der zweite Weg ist der vorgesehene Dauerbetrieb: Was sich als wiederkehrender
Stolperstein herausstellt, wird ergänzt und hilft dem nächsten Projekt.
