# Bauweg — wie diese Unterlagen zu benutzen sind

Alle Bezeichner — Tabellen, Felder, Beziehungen, Layouts, Skripte — sind
**deutsch**. Formeln und Skriptschritte stehen mit deutschen Funktionsnamen, mit
englischer Entsprechung in einer Tabelle daneben, falls Ihre FileMaker-Oberfläche
englisch läuft.

## Methode: rückwärts gebaut

Diese Unterlagen sind **von den fertigen Bildschirmen her entwickelt**, nicht vom
Datenmodell her. Die Reihenfolge war:

1. Was sieht die forschende Person, was sieht die Forschungsleitung? → [`01-bildschirme.md`](01-bildschirme.md)
2. Welche Werte müssen dafür auf dem Schirm stehen? → daraus die Feldliste
3. Woher kommt jeder dieser Werte? → daraus Beziehungen und Formeln
4. Was muss passieren, damit sie entstehen? → daraus die Skripte

Der Vorteil: Es gibt kein Feld ohne Verwendung und keinen Bildschirm, dem ein Wert
fehlt.

## Aufwandsverteilung

| Was | Wie | Aufwand |
|---|---|---|
| 20 Tabellen mit ~260 Feldern | **Import fertiger Dateien** aus `seed/` erzeugt Tabelle *und* Felder in einem Schritt | gering |
| Feldtypen korrigieren | Liste in Doc 02 §22 | mittel, stumpf |
| Berechnungsfelder | 23 Formeln aus Doc 04, fertig formuliert | gering |
| Beziehungen | 26 Bezüge in 33 Tabellenauftreten, Doc 03 | mittel |
| Wertelisten | 15 Stück, Doc 02 §21 | gering |
| **Rechtesets und Zugriffstrennung** | 3 Sets, 2 Formeln, Doc 07 | mittel |
| Skripte | 11 Stück, Schritt für Schritt in Doc 05 | **der größte Posten** |
| Layouts | 6 Arbeits- + 10 Hilfslayouts, Koordinaten in Doc 06 | mittel |
| Inhalte der Wissensschicht | **kommt komplett mit dem Import** | keiner |

Die Wissensschicht — 5 Abschnitte, 13 Schritte, 59 Checklistenpunkte und 7
Hinweisregeln samt allen Erklärtexten in beiden Fassungen — ist bereits
geschrieben und liegt in `seed/`. Das ist der Teil, der sonst Wochen kostet.

## Der Import-Trick

FileMaker kann beim Importieren **eine neue Tabelle mitsamt Feldern anlegen**.
Alle Dateien in `seed/` sind so gebaut, dass genau das funktioniert:

- Dateiname = Tabellenname
- erste Zeile = Feldnamen
- Tabulator-getrennt, UTF-8, keine Zeilenumbrüche in Feldern

```
Datei ▸ Datensätze importieren ▸ Datei…
  Dateityp: Tabulatorgetrennte Textdateien
  Ziel: ◉ Neue Tabelle
  Zeichensatz: Unicode (UTF-8)
  ☑ Erste Datenzeile enthält Feldnamen
```

Danach heißt die Tabelle wie die Datei und hat alle Felder — **alle als Text**.
Die Typkorrektur steht in Doc 02 §22.

Für Tabellen ohne Startdaten (`Aufgaben`, `Hinweise`, …) liegt eine Datei mit nur
der Kopfzeile bereit. Import erzeugt Tabelle und Felder, aber keine Datensätze.

**Was der Import nicht kann:** Berechnungs-, Container- und globale Felder. Die
sind in Doc 02 mit ⊕ markiert und werden von Hand ergänzt.

## Bauabfolge

| # | Schritt | Unterlage |
|---|---|---|
| 1 | Datei anlegen | Doc 08 §1 |
| 2 | Alle `seed/`-Dateien importieren | Doc 08 §2 |
| 3 | Feldtypen korrigieren, ⊕-Felder ergänzen | Doc 02 §22 |
| 4 | Wertelisten anlegen | Doc 02 §21 |
| 5 | Beziehungsdiagramm aufbauen | Doc 03 |
| 6 | Berechnungsformeln einsetzen | Doc 04 |
| 7 | Konten, Rechtesets, Zugriffstrennung | Doc 07 |
| 8 | Hilfslayouts anlegen (90–99) | Doc 06 §8 |
| 9 | Skripte schreiben | Doc 05 |
| 10 | Arbeitslayouts bauen (01–06) | Doc 06 |
| 11 | Serverzeitpläne einrichten | Doc 08 §4 |
| 12 | Testlauf | Doc 08 §6 und Doc 07 §7 |

Schritte 1–6 ergeben eine funktionierende Datenbank ohne Oberfläche. Ab Schritt 10
ist sie benutzbar.

**Schritt 7 nicht nach hinten schieben.** Die Zugriffstrennung hängt an einem
globalen Feld, das Skript S-01 füllt — wer die Rechte erst nach den Skripten
einrichtet, sucht Fehler an der falschen Stelle.

**Wichtig:** Ab dem ersten Echtdatensatz nicht mehr an der Live-Datei arbeiten.
Änderungen an einer Entwicklungskopie vornehmen und mit dem *FileMaker Data
Migration Tool* zurückspielen.

## Namenskonvention

```
__pkProjektID    Primärschlüssel (Text, Hole(UUID))
_fkProjektID     Fremdschlüssel
Titel            normales Feld
c_Standortsatz   Berechnung, nicht gespeichert
k_Eins           Berechnung, gespeichert (Konstante für Bezüge)
s_Zustand        per Skript gesetzter, gespeicherter Wert
s_ZugriffIDs     trägt die Zugriffstrennung
g_MeineStufe     globales Feld (gilt je Sitzung!)
zz_Einstellungen Hilfstabelle
```

Der Unterschied zwischen `c_` und `s_` ist der Kern der Leistungsstrategie:
`c_` rechnet live und stimmt immer, ist aber in Listen langsam und nicht
sortierbar. `s_` wird nachts vom Server geschrieben und ist beides.
Detailansichten zeigen `c_`, Listen zeigen `s_`.

## Die Unterlagen

| Datei | Inhalt |
|---|---|
| [`01-bildschirme.md`](01-bildschirme.md) | Die sechs Bildschirme und was auf ihnen steht — Ausgangspunkt von allem |
| [`02-tabellen-und-felder.md`](02-tabellen-und-felder.md) | 20 Tabellen, alle Felder mit Typ und Herkunft, 15 Wertelisten |
| [`03-beziehungen.md`](03-beziehungen.md) | Beziehungsdiagramm, 26 Bezüge mit Formeln |
| [`04-formeln.md`](04-formeln.md) | 23 Formeln und eine benutzerdefinierte Funktion zum Einfügen |
| [`05-skripte.md`](05-skripte.md) | 11 Skripte, Schritt für Schritt |
| [`06-layouts.md`](06-layouts.md) | 6 Arbeits- und 10 Hilfslayouts mit Koordinaten und Formatierung |
| [`07-rechte-und-mehrbenutzer.md`](07-rechte-und-mehrbenutzer.md) | **Sichttrennung Forschende / Leitung, Konten, Mehrbenutzerbetrieb** |
| [`08-import-server-test.md`](08-import-server-test.md) | Import, Serverzeitpläne, Sicherung, Testlauf |
