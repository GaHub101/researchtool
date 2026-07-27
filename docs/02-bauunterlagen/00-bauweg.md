# Bauweg — wie diese Unterlagen zu benutzen sind

## Methode: rückwärts gebaut

Diese Unterlagen sind **von den fertigen Bildschirmen her entwickelt**, nicht vom
Datenmodell her. Die Reihenfolge war:

1. Was sieht die forschende Person, was sieht die Forschungsleitung? → [`01-bildschirme.md`](01-bildschirme.md)
2. Welche Werte müssen dafür auf dem Schirm stehen? → daraus die Feldliste
3. Woher kommt jeder dieser Werte? → daraus Beziehungen und Formeln
4. Was muss passieren, damit sie entstehen? → daraus die Skripte

Der Vorteil: Es gibt kein Feld ohne Verwendung und keinen Bildschirm, dem ein Wert
fehlt. Jedes Feld in [`02-tabellen-und-felder.md`](02-tabellen-und-felder.md) trägt
in der Spalte *Herkunft* den Bildschirm, für den es existiert.

## Aufwandsverteilung

Der Bauaufwand in FileMaker ist auf das Nötigste reduziert:

| Was | Wie | Aufwand |
|---|---|---|
| 20 Tabellen mit ~250 Feldern | **Import fertiger Dateien** aus `seed/` erzeugt Tabelle *und* Felder in einem Schritt | gering |
| Feldtypen korrigieren | Liste in Doc 02, Spalte *Typ* | mittel, stumpf |
| Berechnungsfelder | Formeln aus Doc 04 einfügen — fertig formuliert | gering |
| Beziehungen | 26 Bezüge in 33 Tabellenauftreten, Formeln in Doc 03 | mittel |
| Wertelisten | 15 Stück, Werte in Doc 02 | gering |
| Skripte | 9 Stück, Schritt für Schritt in Doc 05 | **der größte Posten** |
| Layouts | 6 Arbeitslayouts + 9 Hilfslayouts, Koordinaten in Doc 06 | mittel |
| Inhalte der Wissensschicht | **kommt komplett mit dem Import** | keiner |

Die Wissensschicht — 5 Abschnitte, 13 Schritte, 59 Checklistenpunkte samt allen
Erklärtexten in beiden Fassungen — ist bereits geschrieben und liegt in `seed/`.
Das ist der Teil, der sonst Wochen kostet.

## Der Import-Trick

FileMaker kann beim Importieren **eine neue Tabelle mitsamt Feldern anlegen**.
Alle Dateien in `seed/` sind so gebaut, dass genau das funktioniert:

- Dateiname = Tabellenname
- erste Zeile = Feldnamen
- Tabulator-getrennt, UTF-8, keine Zeilenumbrüche in Feldern

**Ablauf je Datei:**

```
Datei ▸ Datensätze importieren ▸ Datei…
  Dateityp: Tabulatorgetrennte Textdateien
  Datei wählen
  Ziel: ▸ Neue Tabelle
  Zeichensatz: Unicode (UTF-8)
  ☑ Erste Datenzeile enthält Feldnamen
  Importieren
```

Danach heißt die Tabelle wie die Datei und hat alle Felder — **alle als Text**.
Die Typkorrektur steht in Doc 02.

Für Tabellen ohne Startdaten (`Tasks`, `Nudges`, …) liegt eine Datei mit nur der
Kopfzeile bereit. Import erzeugt Tabelle und Felder, aber keine Datensätze.

**Was der Import nicht kann:** Berechnungs-, Container-, Zusammenfassungs- und
globale Felder. Die sind in Doc 02 mit ⊕ markiert und werden von Hand ergänzt.

## Bauabfolge

| # | Schritt | Unterlage |
|---|---|---|
| 1 | Datei anlegen, Konten und Rechtesets | Doc 07 |
| 2 | Alle `seed/`-Dateien importieren | Doc 00 (hier), Doc 07 |
| 3 | Feldtypen korrigieren, ⊕-Felder ergänzen | Doc 02 |
| 4 | Wertelisten anlegen | Doc 02, Abschnitt 21 |
| 5 | Beziehungsdiagramm aufbauen | Doc 03 |
| 6 | Berechnungsformeln einsetzen | Doc 04 |
| 7 | Hilfslayouts anlegen (90–98) | Doc 06, Abschnitt 8 |
| 8 | Skripte schreiben | Doc 05 |
| 9 | Arbeitslayouts bauen (01–06) | Doc 06 |
| 10 | Serverzeitpläne einrichten | Doc 07 |
| 11 | Testlauf | Doc 07, Abschnitt 6 |

Schritte 1–6 ergeben eine funktionierende Datenbank ohne Oberfläche. Ab Schritt 9
ist sie benutzbar.

**Wichtig:** Ab dem ersten Echtdatensatz nicht mehr an der Live-Datei arbeiten.
Änderungen an einer Entwicklungskopie vornehmen und mit dem *FileMaker Data
Migration Tool* zurückspielen.

## Namenskonvention

```
__pkProjectID    Primärschlüssel (Text, Get(UUID))
_fkProjectID     Fremdschlüssel
Titel            normales Feld
c_Standortsatz   Berechnung, nicht gespeichert
k_Eins           Berechnung, gespeichert (Konstante für Bezüge)
s_Zustand        per Skript gesetzter, gespeicherter Wert
g_MeineStufe     globales Feld
zz_Utility       Hilfstabelle
```

Der Unterschied zwischen `c_` und `s_` ist der Kern der Performance-Strategie:
`c_` rechnet live und stimmt immer, ist aber in Listen langsam und nicht
sortierbar. `s_` wird nachts vom Server geschrieben und ist beides. Detailansichten
zeigen `c_`, Listen zeigen `s_`.

## Die Unterlagen

| Datei | Inhalt |
|---|---|
| [`01-bildschirme.md`](01-bildschirme.md) | Die sechs Bildschirme und was auf ihnen steht — Ausgangspunkt von allem |
| [`02-tabellen-und-felder.md`](02-tabellen-und-felder.md) | 20 Tabellen, alle Felder mit Typ und Herkunft, 15 Wertelisten |
| [`03-beziehungen.md`](03-beziehungen.md) | Beziehungsdiagramm, 26 Bezüge mit Formeln |
| [`04-formeln.md`](04-formeln.md) | 23 Formeln und eine Custom Function zum Einfügen |
| [`05-scripts.md`](05-scripts.md) | 9 Skripte, Schritt für Schritt |
| [`06-layouts.md`](06-layouts.md) | 6 Arbeits- und 9 Hilfslayouts mit Koordinaten und Formatierung |
| [`07-rechte-server-import.md`](07-rechte-server-import.md) | Konten, Rechtesets, Serverzeitpläne, Importanleitung, Testlauf |
