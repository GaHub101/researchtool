# Forschungsprojekt-Management-Tool (FileMaker)

Spezifikation und Bauunterlagen für ein System zur Planung, Steuerung und
Überwachung wissenschaftlicher Forschungsprojekte mit klinischem Schwerpunkt.

## Dokumente

| Datei | Inhalt |
|---|---|
| [`docs/00-kompendium-fachspezifikation.md`](docs/00-kompendium-fachspezifikation.md) | Fachliche Quellspezifikation: Zielbild, Prozess, Datenmodell, Module, Geschäftsregeln |
| [`docs/01-umsetzungsplan-filemaker.md`](docs/01-umsetzungsplan-filemaker.md) | Technischer Umsetzungsplan für FileMaker: Leitgedanke, Architekturentscheidungen und Bau-Reihenfolge |

## Leitgedanke

Das System **begleitet, es kontrolliert nicht.** Es richtet sich an unerfahrene
Forschende und unerfahrene Forschungsleitende und zeigt ihnen, wie Forschung
üblicherweise abläuft und woran als Nächstes zu denken ist. Nichts blockiert,
alles lässt sich überspringen, jeder Hinweis lässt sich wegklicken.

Das Fachwissen des Kompendiums wird vollständig übernommen — aber als Inhalt,
nicht als Schranke.

## Rahmen

- **Plattform:** FileMaker Pro + FileMaker Server, Mehrbenutzerbetrieb
- **Datenumfang:** ausschließlich Steuerungs- und Metadaten, keine personenbezogenen Studiendaten
- **Erste Ausbaustufe:** MVP nach Kompendium Kapitel 14.1, ergänzt um die Orientierungsschicht

## Stand

Planung abgeschlossen, Umsetzung noch nicht begonnen. Die Etappen werden
nacheinander spezifiziert und gebaut — siehe Kapitel 11 des Umsetzungsplans.
