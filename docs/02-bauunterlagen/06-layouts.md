# Layouts

**6 Arbeitslayouts, 10 Hilfslayouts.** Alle Arbeitslayouts 1000 pt breit, Thema
*Minimal*, Schriftart durchgehend eine humanistische Grotesk (Helvetica Neue,
Segoe UI oder Inter), Grundgröße 12 pt.

Koordinaten in Punkt, Ursprung links oben im jeweiligen Layoutteil.

## Farbwerte

| Zweck | Wert |
|---|---|
| Text | `#1A1A1A` |
| Text zweite Ebene | `#6B6B6B` |
| Erklärtexte | `#7A7A7A` |
| Linien und Rahmen | `#E0E0E0` |
| Flächen (Hinweiskasten, Überschriftszeile) | `#F5F5F3` |
| Akzent (Balken, aktiver Reiter) | `#4A6FA5` |
| „stockt" | `#B8860B` |
| „abgeschlossen" | `#4A7C59` |

**Kein Rot im gesamten System.** Das ist keine Geschmacksfrage, sondern der
Leitgedanke: Verzug erscheint als verschobenes Datum, nicht als Warnfarbe.

---

## 1 · Layout `01 Mein Projekt`

Kontext **Projekte** · Formularansicht · 1000 × 720

### Kopfteil (Höhe 110)

| Objekt | Inhalt | x | y | b | h | Format |
|---|---|---|---|---|---|---|
| Titel | `Projekte::Titel` | 20 | 16 | 700 | 30 | 22 pt, halbfett |
| Code | `Projekte::c_Projektcode` | 820 | 20 | 160 | 20 | 12 pt, rechtsbündig, `#6B6B6B` |
| **Standortsatz** | `Projekte::c_Standortsatz` | 20 | 52 | 960 | 24 | **15 pt** |
| Balken | `Projekte::c_ZeitfortschrittProzent` | 20 | 84 | 380 | 14 | Steuerungsstil **Balkendiagramm**, 0–100, Farbe Akzent |
| Prognose | Textobjekt `Einreichung voraussichtlich <<Projekte::c_EinreichungText>>` | 416 | 82 | 400 | 18 | 12 pt, `#6B6B6B` |
| Trennlinie | Linie | 0 | 109 | 1000 | 1 | `#E0E0E0` |

Der Standortsatz ist bewusst das größte Element nach dem Titel.

### Hauptteil (Höhe 530)

**Links — der laufende Abschnitt**

| Objekt | Inhalt | x | y | b | h |
|---|---|---|---|---|---|
| Portal `p_abschnitt` | B-02, Filter **F-20.3**, 1 Zeile, ohne Rahmen | 20 | 14 | 620 | 52 |
| ↳ Abschnittsname | `Abschnitt <<Abschnitte::Nr>> — <<Abschnitte::Name>>` | 8 | 4 | 600 | 22 |
| ↳ Leitfrage | `Abschnitte::Leitfrage` | 8 | 26 | 600 | 18 |
| Portal `p_checkliste` | B-04, Filter **F-20.5**, Sortierung `SchrittNr, Nr`, Zeilenhöhe 46, Bildlaufleiste | 20 | 76 | 620 | 400 |
| ↳ Kontrollkästchen | `Punkte::Erledigt` | 8 | 8 | 18 | 18 |
| ↳ Punkt | `Punkte::Punkt` | 34 | 6 | 560 | 18 |
| ↳ Erklärung | `Punkte::Erklaerung` | 34 | 24 | 560 | 18 |

Am Portal `p_checkliste` einstellen:

- **Kontrollkästchen** → *Objekt ▸ Ausblenden wenn* `Punkte::IstUeberschrift = 1`
- **Erklärung** → *Ausblenden wenn* `Punkte::IstUeberschrift = 1`
- **Zeile** → bedingte Formatierung nach F-21 (Überschrift fett auf `#F5F5F3`,
  Erledigtes grau, „trifft nicht zu" durchgestrichen)
- **Kontrollkästchen** → Skriptauslöser *BeiObjektänderung* → S-09 Verlauf schreiben

> Die Überschriftszeilen sind gewöhnliche Portalzeilen mit
> `IstUeberschrift = 1` — der Ersatz für verschachtelte Portale, die FileMaker
> nicht kann. Siehe F-11.

**Rechts — Hinweise**

| Objekt | Inhalt | x | y | b | h |
|---|---|---|---|---|---|
| Beschriftung | Text „Hinweise" | 664 | 14 | 200 | 18 |
| Portal `p_hinweise` | B-09, Filter **F-20.1**, Zeilenhöhe 112, Fläche `#F5F5F3` | 660 | 38 | 320 | 438 |
| ↳ Titel | `Hinweise::Titel` | 10 | 8 | 296 | 18 |
| ↳ Text | `Hinweise::Text` | 10 | 28 | 296 | 60 |
| ↳ Knopf | „passt" → **S-06** | 240 | 88 | 66 | 20 |

**Unten — meine Aufgaben**

| Objekt | Inhalt | x | y | b | h |
|---|---|---|---|---|---|
| Beschriftung | „Meine offenen Aufgaben" | 20 | 486 | 300 | 18 |
| Portal `p_aufgaben` | B-06, Filter **F-20.2**, Zeilenhöhe 22 | 20 | 508 | 960 | 90 |
| ↳ Titel | `Aufgaben::Titel` | 8 | 2 | 700 | 18 |
| ↳ Termin | `fällig <<Aufgaben::Termin>>` | 780 | 2 | 160 | 18 |

---

## 2 · Layout `02 Meine Projekte`

Kontext **Projekte** · **Listenansicht** · 1000 breit

> **Nur für die Stufe Leitung.** Im Rechteset `Forschende` steht dieses Layout —
> und `02b` — auf *kein Zugriff* (Doc 07 §2). Das ist Kosmetik; die eigentliche
> Trennung liegt in den Datensatzrechten.

### Kopfteil (Höhe 60)

Überschrift „Meine Projekte" (20 pt) links, `Hole ( AnzahlGefundene ) & " Projekte"`
rechts, darunter die Spaltenköpfe Projekt · Bearbeitung · Stand · Einreichung.
Spaltenköpfe als Knöpfe mit *Datensätze sortieren* — deshalb liest diese Liste
`s_`-Felder: ungespeicherte Berechnungen ließen sich nicht sortieren.

### Datenteil (Höhe 44)

| Objekt | Inhalt | x | y | b | h | Format |
|---|---|---|---|---|---|---|
| Titel | `Projekte::Titel` | 20 | 6 | 340 | 18 | Knopf → S-„Projektakte öffnen" |
| Bearbeitung | `Projekte_Beteiligte_Personen::c_Kurzname` | 380 | 6 | 140 | 18 | |
| Stand | `"M " & Projekte::s_MonatImProjekt & "/" & Projekte::BogenLaengeMonate` | 540 | 6 | 90 | 18 | |
| Zustand | `Projekte::s_Zustand` | 540 | 24 | 90 | 14 | 10 pt, bedingt nach F-21 |
| Einreichung | `Projekte::c_EinreichungText` | 660 | 6 | 140 | 18 | |
| Trennlinie | Linie | 20 | 43 | 960 | 1 | `#E0E0E0` |

### Abschlussteil (Höhe 260) — „Was sich zu besprechen lohnt"

Der eigentliche Grund für die zweite Stufe.

| Objekt | Inhalt | x | y | b | h |
|---|---|---|---|---|---|
| Beschriftung | „Was sich zu besprechen lohnt" | 20 | 16 | 400 | 20 |
| Portal `p_besprechen` | B-09, Filter **F-20.1**, Zeilenhöhe 78, sortiert nach `Rang` | 20 | 44 | 960 | 200 |
| ↳ Projekt · Person | `<<Hinweise::…>>` Kopfzeile | 10 | 6 | 940 | 16 |
| ↳ Text | `Hinweise::Text` | 10 | 24 | 940 | 44 |

> Der Abschlussteil zeigt Portalzeilen des **aktuellen** Datensatzes. Damit hier
> projektübergreifend gesammelt wird, ist dieser Bereich als eigenes Layout
> `02b Besprechungsliste` auf Kontext `Hinweise` zu bauen — Listenansicht, Suchlauf
> `Stufe = "Leitung"`, `Weggeklickt = 0`, `Zurueckgezogen = 0`, sortiert nach
> `Rang`. Der Knopf „Besprechungsliste" im Kopfteil von `02` führt dorthin.
> Ein Portal kann nicht über mehrere Projekte hinweg zeigen — hier gibt es keinen
> Trick, sondern ein zweites Layout.

---

## 3 · Layout `03 Projektakte`

Kontext **Projekte** · Formularansicht · 1000 × 700

**Kopfteil (Höhe 78):** Zurück-Knopf, Titel, Code, Zustand; darunter eine Zeile
`Monat <<s_MonatImProjekt>> von <<BogenLaengeMonate>> · <<s_AktuellerAbschnittName>> · Einreichung voraussichtlich <<c_EinreichungText>>`
in 12 pt, `#6B6B6B`.

**Hauptteil:** ein **Registerkartenobjekt** x20 y10 b960 h590 mit sieben Reitern:

| Reiter | Portal | Bezug | Filter |
|---|---|---|---|
| Abschnitte | `p_abschnitte` | B-02 | — |
| Ergebnisse | `p_ergebnisse` | B-05 | — |
| Aufgaben | `p_aufgaben` | B-06 | — |
| Dokumente | `p_dokumente` | B-07 | — |
| Risiken | `p_risiken` | B-08 | — |
| Publikation | `p_manuskripte` | B-11 | — |
| Verlauf | `p_verlauf` | B-10 | — |

Portal *Abschnitte*, Zeilenhöhe 30:

| Objekt | Inhalt | x | b |
|---|---|---|---|
| Nr | `Abschnitte::Nr` | 10 | 24 |
| Name | `Abschnitte::Name` | 44 | 260 | Knopf → S-„Abschnitt öffnen" |
| Monate | `"M " & Abschnitte::Band_Von_Monat & "–" & Abschnitte::Band_Bis_Monat` | 320 | 90 |
| Status | `Abschnitte::Status` | 430 | 120 |
| Fortschritt | `Abschnitte::c_PunkteErledigt & "/" & Abschnitte::c_PunkteGesamt` | 570 | 70 |

Bedingte Formatierung: laufender Abschnitt fett (F-21).

---

## 4 · Layout `04 Abschnitt`

Kontext **Abschnitte** · Formularansicht · 1000 × 700

**Kopfteil (Höhe 130):**

| Objekt | Inhalt | x | y | b | h | Format |
|---|---|---|---|---|---|---|
| Zurück | Knopf „‹ Projektakte" | 20 | 16 | 120 | 20 | |
| Überschrift | `Abschnitt <<Abschnitte::Nr>> — <<Abschnitte::Name>>` | 160 | 12 | 500 | 26 | 18 pt halbfett |
| Monate | `"M " & Band_Von_Monat & "–" & Band_Bis_Monat` | 700 | 16 | 100 | 18 | `#6B6B6B` |
| Status | `Abschnitte::Status` | 820 | 16 | 100 | 18 | |
| Leitfrage | `Abschnitte::Leitfrage` | 20 | 46 | 700 | 20 | 14 pt |
| Knopf | „Abschnitt starten" → **S-04** Parameter `"start"` | 760 | 46 | 220 | 24 | *Ausblenden wenn* `Status ≠ "offen"` |
| Knopf | „Abschnitt abschließen" → **S-04** Parameter `"ende"` | 760 | 46 | 220 | 24 | *Ausblenden wenn* `Status ≠ "läuft"` |

**Hauptteil, Wissensbereich (y 0–150):**

| Objekt | Inhalt | x | y | b | h |
|---|---|---|---|---|---|
| Überschrift | „Wozu dieser Abschnitt da ist" | 20 | 12 | 400 | 18 |
| Text | `Abschnitte::Zweck` | 20 | 32 | 620 | 54 |
| Überschrift | „Was hier erfahrungsgemäß schiefgeht" | 20 | 94 | 400 | 18 |
| Text | `Abschnitte::Stolpersteine` | 20 | 114 | 620 | 54 |
| **Kasten Leitung** | Fläche `#F5F5F3` + „Worauf zu schauen ist" + `Abschnitte::Hinweis_Leitung` | 664 | 12 | 316 | 156 |

Am Kasten: *Objekt ▸ Ausblenden wenn* `Projekte::g_MeineStufe ≠ "Leitung"`.
Das ist die einzige Stelle im System, an der die beiden Stufen unterschiedliche
Objekte sehen statt nur unterschiedlicher Texte.

**Hauptteil, Arbeitsbereich (y 180–560):**

| Objekt | Bezug | x | y | b | h |
|---|---|---|---|---|---|
| Portal `p_checkliste` | B-18, Sortierung `SchrittNr, Nr`, Zeilenhöhe 46 | 20 | 180 | 620 | 370 |
| Portal `p_ergebnisse` | B-19, Filter F-20.7 | 664 | 180 | 316 | 180 |
| Portal `p_aufgaben` | B-20 | 664 | 372 | 316 | 178 |

Portalzeile der Checkliste wie in Layout 01, zusätzlich ein Notizknopf x596
→ öffnet ein **Kartenfenster** mit `Punkte::Notiz`.

---

## 5 · Layout `05 Zeitachse`

Kontext **Projekte** · Formularansicht · 1000 × 480

Die Balken entstehen als **Zeichenketten in einer nichtproportionalen Schrift**,
nicht als gezeichnete Rechtecke. Grund: FileMaker kann die Breite eines Objekts
nicht berechnen lassen. Zwei Zeichen je Monat, 24 Monate = 48 Zeichen.

Schrift für die Balkenfelder: **Menlo**, **Consolas** oder **Courier New**, 11 pt.

| Objekt | Inhalt | x | y | b | h |
|---|---|---|---|---|---|
| Monatsskala | Textobjekt `1····6····12····18····24` (monospace) | 240 | 40 | 600 | 16 |
| Portal `p_zeitachse` | B-02, Zeilenhöhe 40, ohne Rahmen | 20 | 64 | 960 | 220 |
| ↳ Name | `Abschnitte::Nr & " " & Abschnitte::Name` | 8 | 6 | 200 | 18 |
| ↳ Soll-Balken | `Abschnitte::c_BandGrafik` | 220 | 4 | 600 | 16 |
| ↳ Ist-Balken | `Abschnitte::c_IstGrafik` | 220 | 20 | 600 | 16 |
| Legende | `▓ geplant   █ tatsächlich` | 220 | 300 | 400 | 18 |

Formeln F-22 und F-23 in [`04-formeln.md`](04-formeln.md), dazu die
benutzerdefinierte Funktion **CF-01 `Wiederhole`**.

> Eine grafische Variante mit echten Rechtecken ist möglich, aber teuer: man
> müsste 24 vorgezeichnete Segmente je Zeile ablegen und einzeln über
> *Ausblenden wenn* steuern. Die Zeichenkettenvariante sieht in einer
> nichtproportionalen Schrift ordentlich aus und kostet zwei Formeln.

---

## 6 · Layout `06 Projektauswahl`

Kontext **Projekte** · Listenansicht · Titel, Standortsatz gekürzt, Zustand.
Zeile als Knopf → Layout `01` bzw. `03`. Im Kopfteil ein Knopf
**„Neues Projekt"** → S-03.

---

## 7 · Navigation

Eine gemeinsame Kopfleiste gibt es bewusst nicht — beide Stufen haben genau einen
Einstieg und kommen mit Zurück-Knöpfen aus. Ein Menü wäre für sechs Layouts
Overhead.

| Von | Knopf | Nach |
|---|---|---|
| 01 | Projektakte | 03 |
| 02 | Projektzeile | 03 |
| 02 | Besprechungsliste | 02b |
| 03 | Abschnittszeile | 04 |
| 03 | Zeitachse | 05 |
| 04 | ‹ Projektakte | 03 |
| 06 | Projektzeile | 01 |

---

## 8 · Hilfslayouts

Zehn leere Listenlayouts, je Tabelle eines, **ohne Objekte**. Sie dienen nur
Skripten als Kontext zum Anlegen von Datensätzen. In den Layout-Einstellungen
*Im Layout-Menü anzeigen* **abschalten**.

`90 Projekte_Hilfslayout` · `91 Abschnitte_Hilfslayout` ·
`92 Schritte_Hilfslayout` · `93 Punkte_Hilfslayout` ·
`94 VorlageAbschnitte_Hilfslayout` · `95 VorlageSchritte_Hilfslayout` ·
`96 VorlagePunkte_Hilfslayout` · `97 Hinweise_Hilfslayout` ·
`98 Verlauf_Hilfslayout` · `99 Beteiligte_Hilfslayout`

---

## 9 · Bauhinweise

- **Zuerst** die neun Hilfslayouts anlegen — ohne sie laufen die Skripte nicht.
- Portale immer benennen (*Objektname* in der Inspektor-Palette), sonst greift
  `Refresh Portal` in S-06 ins Leere.
- Bei allen Feldern *Eingabe erlauben im Suchmodus* aktiv lassen; die Suchläufe
  in S-02 und S-08 brauchen das.
- Registerkartenobjekte in `03` erst bauen, wenn alle Portale einzeln
  funktionieren — Fehlersuche innerhalb von Reitern ist mühsam.
- Kein Feld auf einem Arbeitslayout ist schreibgeschützt. Auch Statusfelder
  nicht: Die Skripte setzen sie, aber niemand ist daran gebunden.
- In den Layout-Einstellungen *Datensatz beim Verlassen speichern* ohne Rückfrage
  aktivieren. Im Mehrbenutzerbetrieb verhindert das offen bleibende
  Datensatzsperren (Doc 07 §6).
- Knopf **„Neues Projekt"** auf Layout `06` mit *Ausblenden wenn*
  `Projekte::g_MeineStufe ≠ "Leitung"` versehen — Projekte legt die Leitung an.
