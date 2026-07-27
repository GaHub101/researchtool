# Forschungsprojekt-Management-Tool (FileMaker)

Spezifikation und Bauunterlagen für ein System zur Planung, Steuerung und
Überwachung wissenschaftlicher Forschungsprojekte mit klinischem Schwerpunkt.

## Dokumente

| Datei | Inhalt |
|---|---|
| [`docs/00-kompendium-fachspezifikation.md`](docs/00-kompendium-fachspezifikation.md) | Fachliche Quellspezifikation: Zielbild, Prozess, Datenmodell, Module, Geschäftsregeln |
| [`docs/01-umsetzungsplan-filemaker.md`](docs/01-umsetzungsplan-filemaker.md) | Umsetzungsplan: Leitgedanke, Architekturentscheidungen, Bau-Reihenfolge |
| [`docs/02-bauunterlagen/`](docs/02-bauunterlagen/) | **Bauunterlagen** — Bildschirme, Tabellen, Beziehungen, Formeln, Skripte, Layouts, Konten |
| [`seed/`](seed/) | **Import-fertige Startdaten** inklusive der kompletten Wissensschicht |

Einstieg in den Bau: [`docs/02-bauunterlagen/00-bauweg.md`](docs/02-bauunterlagen/00-bauweg.md)

## Leitgedanke

Das System **begleitet sanft durch zwei Jahre Forschungsarbeit.** Es zeigt, wie
ein Projekt üblicherweise verläuft und woran als Nächstes zu denken ist. Nichts
blockiert, alles lässt sich überspringen, jeder Hinweis lässt sich wegklicken.

Das Fachwissen des Kompendiums wird vollständig übernommen — aber als Inhalt,
nicht als Schranke.

## Zwei Stufen

| Stufe | Anzahl | Leitfrage | Sieht |
|---|---|---|---|
| **Forschende** | 6–9 | Wo stehe ich, und was mache ich als Nächstes? | nur eigene Projekte |
| **Forschungsleitung** | 2–3 | Wie stehen meine Projekte, und wo sollte ich mich einmischen? | alle Projekte |

Zwei Aufbereitungen derselben Daten — bis hinein in die hinterlegten Erklärtexte,
die für jede Stufe eigens formuliert sind.

Die Sichttrennung liegt im **Rechtesystem** der Datenbank, nicht in der
Oberfläche: Sie greift auch beim Export und in selbstgebauten Ansichten.

## Der Bogen über 24 Monate

Ein Ablaufmodell für alle Vorhaben, unabhängig vom Studiendesign:

| Abschnitt | Leitfrage | Monate |
|---|---|---|
| Klären | Was will ich herausfinden, und geht das überhaupt? | 1 – 4 |
| Planen | Wie gehe ich vor, und darf ich das? | 4 – 9 |
| Sammeln | Woher kommt mein Material? | 8 – 16 |
| Auswerten | Was sagen die Daten? | 15 – 19 |
| Veröffentlichen | Wer soll davon erfahren? | 18 – 24 |

Die feineren Schritte darunter lassen sich einzeln als „trifft nicht zu"
markieren — so passt ein Modell auf jedes Vorhaben.

## Rahmen

- **Plattform:** FileMaker Pro + FileMaker Server, 8–12 gleichzeitige Anwender
- **Sprache:** alle Bezeichner, Formeln und Oberflächen deutsch
- **Datenumfang:** ausschließlich Steuerungs- und Metadaten, keine personenbezogenen Studiendaten
- **Erste Ausbaustufe:** MVP nach Kompendium Kapitel 14.1, ergänzt um Zeitachse und Orientierungsschicht

## Stand

Planung und Bauunterlagen abgeschlossen, Nachbau in FileMaker noch nicht begonnen.

`.fmp12`-Dateien lassen sich nur in FileMaker Pro selbst erzeugen. Deshalb liegt
hier alles, was sich vorbereiten lässt: 20 Tabellen als import-fertige Dateien,
26 Beziehungen mit Formeln, 23 Berechnungen zum Einfügen, 11 Skripte Schritt für
Schritt, 16 Layouts mit Koordinaten, drei Rechtesets samt Zugriffstrennung — und
die komplette Wissensschicht mit 84 redaktionellen Einträgen.

In FileMaker bleibt der Nachbau anhand dieser Unterlagen.
