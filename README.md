# Forschungsprojekt-Management-Tool (FileMaker)

Spezifikation und Bauunterlagen für ein System zur Planung, Steuerung und
Überwachung wissenschaftlicher Forschungsprojekte mit klinischem Schwerpunkt.

## Dokumente

| Datei | Inhalt |
|---|---|
| [`docs/00-kompendium-fachspezifikation.md`](docs/00-kompendium-fachspezifikation.md) | Fachliche Quellspezifikation: Zielbild, Prozess, Datenmodell, Module, Geschäftsregeln |
| [`docs/01-umsetzungsplan-filemaker.md`](docs/01-umsetzungsplan-filemaker.md) | Technischer Umsetzungsplan für FileMaker: Leitgedanke, Architekturentscheidungen und Bau-Reihenfolge |

## Leitgedanke

Das System **begleitet sanft durch zwei Jahre Forschungsarbeit.** Es zeigt, wie
ein Projekt üblicherweise verläuft und woran als Nächstes zu denken ist. Nichts
blockiert, alles lässt sich überspringen, jeder Hinweis lässt sich wegklicken.

Das Fachwissen des Kompendiums wird vollständig übernommen — aber als Inhalt,
nicht als Schranke.

## Zwei Stufen

| Stufe | Leitfrage |
|---|---|
| **Forschende** | Wo stehe ich, und was mache ich als Nächstes? |
| **Forschungsleitung** | Wie stehen meine Projekte, und wo sollte ich mich einmischen? |

Dieselben Daten, zwei Aufbereitungen — bis hinein in die hinterlegten Erklärtexte.

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

- **Plattform:** FileMaker Pro + FileMaker Server, Mehrbenutzerbetrieb
- **Datenumfang:** ausschließlich Steuerungs- und Metadaten, keine personenbezogenen Studiendaten
- **Erste Ausbaustufe:** MVP nach Kompendium Kapitel 14.1, ergänzt um Zeitachse und Orientierungsschicht

## Stand

Planung abgeschlossen, Umsetzung noch nicht begonnen. Die Etappen werden
nacheinander spezifiziert und gebaut — siehe Kapitel 11 des Umsetzungsplans.
