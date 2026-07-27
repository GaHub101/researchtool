# Formeln

Alle Berechnungen zum Einfügen. **Kontext** ist das Tabellenauftreten, das im
Formeleditor oben links eingestellt sein muss — bei falschem Kontext findet
FileMaker die Bezugsfelder nicht.

## Vorbemerkung: Funktionsnamen

Die Formeln stehen mit **deutschen Funktionsnamen**, passend zu einer deutschen
FileMaker-Oberfläche. Sicherheitshalber die Entsprechungen, falls Ihre
Installation englisch läuft:

| Deutsch | Englisch | Deutsch | Englisch |
|---|---|---|---|
| `Setze` | Let | `Hole` | Get |
| `Fallunterscheidung` | Case | `Wenn` | If |
| `IstLeer` | IsEmpty | `Runden` | Round |
| `Jahr` `Monat` `Tag` | Year Month Day | `Datum` | Date |
| `Summe` | Sum | `Max` `Min` | Max Min |
| `HoleAlsZahl` | GetAsNumber | `HoleAlsDatum` | GetAsDate |
| `Rechts` `Links` | Right Left | `Monatsname` | MonthName |
| `Austauschen` | Substitute | `AuswerteSQL` | ExecuteSQL |
| `FilterWerte` | FilterValues | `HoleWert` | GetValue |

Die Struktur der Formeln ist in beiden Sprachen identisch; nur die Namen wechseln.
Im Zweifel die Funktion aus der Liste rechts im Formeleditor doppelklicken — dann
wird immer der richtige Name eingesetzt.

---

## F-01 · `Projekte::c_Projektcode` — Text, nicht gespeichert

```
Jahr ( HoleAlsDatum ( Projekte::ErstelltAm ) ) & "-" & Rechts ( "000" & Projekte::LfdNr ; 3 )
```

Ergibt `2026-014`.

---

## F-02 · `Projekte::c_MonatImProjekt` — Zahl, nicht gespeichert

Der Grundwert des ganzen Systems. Monat 1 ist der Startmonat.

```
Setze ( [
    s = Projekte::Startdatum ;
    h = Hole ( AktuellesDatum )
  ] ;
    Fallunterscheidung (
      IstLeer ( s ) ; "" ;
      ( Jahr ( h ) - Jahr ( s ) ) * 12
      + ( Monat ( h ) - Monat ( s ) )
      + Wenn ( Tag ( h ) ≥ Tag ( s ) ; 1 ; 0 )
    )
)
```

Start 15.01., heute 20.01. → Monat 1. Heute 14.02. → immer noch Monat 1.
Heute 15.02. → Monat 2.

---

## F-03 · `Projekte::c_SollAbschnittNr` — Zahl, nicht gespeichert

In welchem Abschnitt man planmäßig sein *sollte*. Nutzt die überschneidungsfreien
`Schwerpunkt`-Bereiche — mit den überlappenden `Band`-Bereichen wäre die Antwort
mehrdeutig.

```
Setze ( [
    m = Projekte::c_MonatImProjekt ;
    r = AuswerteSQL (
          "SELECT \"Nr\" FROM \"Abschnitte\"
             WHERE \"_fkProjektID\" = ?
               AND \"Schwerpunkt_Von_Monat\" <= ?
               AND \"Schwerpunkt_Bis_Monat\" >= ?" ;
          "" ; "" ;
          Projekte::__pkProjektID ; m ; m )
  ] ;
    Fallunterscheidung ( IstLeer ( r ) ; "" ; HoleAlsZahl ( r ) )
)
```

> `AuswerteSQL` erspart hier ein gefiltertes Tabellenauftreten. Es ist nicht
> gespeichert und in Listen zu langsam — deshalb schreibt Skript S-08 den Wert
> nachts nach `s_AktuellerAbschnittNr`, und Listen lesen nur dort.

## F-04 · `Projekte::c_SollAbschnittName` — Text, nicht gespeichert

```
Setze ( [
    m = Projekte::c_MonatImProjekt ;
    r = AuswerteSQL (
          "SELECT \"Name\" FROM \"Abschnitte\"
             WHERE \"_fkProjektID\" = ?
               AND \"Schwerpunkt_Von_Monat\" <= ?
               AND \"Schwerpunkt_Bis_Monat\" >= ?" ;
          "" ; "" ;
          Projekte::__pkProjektID ; m ; m )
  ] ;
    Austauschen ( r ; ¶ ; "" )
)
```

---

## F-05 · `Projekte::c_Standortsatz` — Text, nicht gespeichert

**Der wichtigste Einzelwert im System.** Steht ganz oben auf jeder Projektakte und
beantwortet die Frage, die sich Unerfahrene nicht zu stellen trauen.

```
Setze ( [
    m     = Projekte::c_MonatImProjekt ;
    n     = Projekte::BogenLaengeMonate ;
    istN  = Projekte::s_AktuellerAbschnittNr ;
    istT  = Projekte::s_AktuellerAbschnittName ;
    sollN = Projekte::c_SollAbschnittNr ;
    sollT = Projekte::c_SollAbschnittName
  ] ;
    Fallunterscheidung (
      IstLeer ( Projekte::Startdatum ) ;
        "Kein Startdatum hinterlegt — ohne das kann das System nicht sagen, wo Sie stehen." ;

      m > n ;
        "Monat " & m & ". Der geplante Bogen von " & n & " Monaten ist abgelaufen. Das kommt häufig vor und ist kein Grund zur Sorge." ;

      IstLeer ( istN ) ;
        "Monat " & m & " von " & n & ". Noch kein Abschnitt begonnen — planmäßig wäre jetzt „" & sollT & "“." ;

      istN = sollN ;
        "Monat " & m & " von " & n & ". Planmäßig im Abschnitt „" & istT & "“ — das passt." ;

      istN < sollN ;
        "Monat " & m & " von " & n & ". Sie sind im Abschnitt „" & istT & "“, planmäßig wäre „" & sollT & "“. Kein Drama — die Einreichung rückt entsprechend nach hinten." ;

      "Monat " & m & " von " & n & ". Sie sind im Abschnitt „" & istT & "“ und damit früher dran als geplant."
    )
)
```

Der Ton ist Absicht: keine Wertung, kein Ausrufezeichen, und im Verzugsfall die
Folge statt eines Vorwurfs.

---

## F-06 · `Projekte::c_ZeitfortschrittProzent` — Zahl, nicht gespeichert

```
Setze ( [
    m = Projekte::c_MonatImProjekt ;
    n = Projekte::BogenLaengeMonate
  ] ;
    Fallunterscheidung (
      IstLeer ( m ) oder n = 0 ; 0 ;
      Min ( 100 ; Runden ( m / n * 100 ; 0 ) )
    )
)
```

Im Layout als Balken darstellen: Feld ▸ *Steuerungsstil* ▸ **Balkendiagramm**,
Bereich 0 bis 100.

---

## F-07 · `Projekte::c_Verzug` — Zahl, nicht gespeichert

Wie viele Monate hinter dem Plan. Basis der Fortschreibung.

```
Setze ( [
    m   = Projekte::c_MonatImProjekt ;
    bis = Projekte::s_AktuellerAbschnittSchwerpunktBis
  ] ;
    Fallunterscheidung (
      IstLeer ( bis ) ; 0 ;
      Max ( 0 ; m - bis )
    )
)
```

Nur nach oben — wer schneller ist, bekommt keinen negativen Verzug angerechnet.
Die Einreichung rückt dadurch nie nach vorne, was der Erfahrung entspricht.

## F-08 · `Projekte::c_EinreichungVoraussichtlich` — Datum, nicht gespeichert

```
Setze ( [
    s = Projekte::Startdatum ;
    z = Projekte::ZielpunktEinreichungMonat ;
    v = Projekte::c_Verzug
  ] ;
    Wenn ( IstLeer ( s ) ; "" ;
      Datum ( Monat ( s ) + z + v ; Tag ( s ) ; Jahr ( s ) )
    )
)
```

`Datum()` rechnet Monatsüberläufe selbst um — Monat 27 wird zu März des Folgejahres.

## F-09 · `Projekte::c_EinreichungText` — Text, nicht gespeichert

```
Setze ( d = Projekte::c_EinreichungVoraussichtlich ;
    Wenn ( IstLeer ( d ) ; "—" ; Monatsname ( d ) & " " & Jahr ( d ) )
)
```

Ergibt `März 2028`. Bewusst ohne Tagesangabe: eine Prognose auf den Tag genau
wäre eine Genauigkeit, die es nicht gibt.

---

## F-10 · `Projekte::c_Zustand` — Text, nicht gespeichert

```
Setze ( [
    st     = Projekte::Status ;
    letzte = Projekte::s_LetzteAktivitaet ;
    tage   = Hole ( AktuellesDatum ) - letzte ;
    f1     = Projekte_Einstellungen::StilleFristTage ;
    f2     = Projekte_Einstellungen::RuhtFristTage
  ] ;
    Fallunterscheidung (
      st = "abgeschlossen" ; "abgeschlossen" ;
      st = "abgebrochen"   ; "abgebrochen" ;
      st = "ruht"          ; "ruht" ;
      IstLeer ( letzte )   ; "läuft" ;
      tage > f2            ; "ruht" ;
      tage > f1            ; "stockt" ;
                             "läuft"
    )
)
```

---

## F-11 · `Punkte` — zwei gespeicherte Zahlenfelder

**Wichtig: bei beiden den Haken bei *Ergebnis nicht speichern* entfernen.** Nur
gespeicherte Felder lassen sich schnell summieren.

`IstOffen`:
```
Wenn (
  Punkte::Erledigt = 1
  oder Punkte::TrifftNichtZu = 1
  oder Punkte::IstUeberschrift = 1
  ; 0 ; 1 )
```

`IstGezaehlt`:
```
Wenn (
  Punkte::TrifftNichtZu = 1
  oder Punkte::IstUeberschrift = 1
  ; 0 ; 1 )
```

---

## F-12 · `c_PunkteGesamt` — Zahl, nicht gespeichert

Kontext **Schritte**: `Summe ( Schritte_Punkte::IstGezaehlt )`

Kontext **Abschnitte**: `Summe ( Abschnitte_Punkte::IstGezaehlt )`

## F-13 · `c_PunkteErledigt` — Zahl, nicht gespeichert

Kontext **Schritte**:
```
Summe ( Schritte_Punkte::IstGezaehlt ) - Summe ( Schritte_Punkte::IstOffen )
```

Kontext **Abschnitte**:
```
Summe ( Abschnitte_Punkte::IstGezaehlt ) - Summe ( Abschnitte_Punkte::IstOffen )
```

Anzeige „3/9" im Layout: `c_PunkteErledigt & "/" & c_PunkteGesamt`

---

## CF-01 · Benutzerdefinierte Funktion `Wiederhole ( text ; anzahl )`

*Datei ▸ Verwalten ▸ Benutzerdefinierte Funktionen ▸ Neu*

```
Fallunterscheidung ( anzahl < 1 ; "" ; text & Wiederhole ( text ; anzahl - 1 ) )
```

Rekursiv, für die Zeitachse. FileMaker hat keine eingebaute Wiederholfunktion;
diese Zeile erspart jede Bastelei.

## F-22 · `Abschnitte::c_BandGrafik` — Text, nicht gespeichert

Der **geplante** Balken der Zeitachse als Zeichenkette, zwei Zeichen je Monat. Im
Layout in einer nichtproportionalen Schrift darstellen (Doc 06 §5).

```
Setze ( [
    n  = Abschnitte_Projekte::BogenLaengeMonate ;
    bv = Abschnitte::Band_Von_Monat ;
    bb = Abschnitte::Band_Bis_Monat
  ] ;
    Fallunterscheidung ( n = 0 ; "" ;
      Wiederhole ( "·" ; ( bv - 1 ) * 2 )
    & Wiederhole ( "▓" ; ( bb - bv + 1 ) * 2 )
    & Wiederhole ( "·" ; ( n - bb ) * 2 )
    )
)
```

## F-23 · `Abschnitte::c_IstGrafik` — Text, nicht gespeichert

Der **tatsächliche** Balken. Läuft der Abschnitt noch, endet er bei heute.

```
Setze ( [
    s  = Abschnitte_Projekte::Startdatum ;
    a  = Abschnitte::Ist_Start ;
    e  = Wenn ( IstLeer ( Abschnitte::Ist_Ende ) ; Hole ( AktuellesDatum ) ; Abschnitte::Ist_Ende ) ;
    n  = Abschnitte_Projekte::BogenLaengeMonate ;
    ma = ( Jahr ( a ) - Jahr ( s ) ) * 12 + ( Monat ( a ) - Monat ( s ) ) ;
    md = ( Jahr ( e ) - Jahr ( a ) ) * 12 + ( Monat ( e ) - Monat ( a ) ) + 1
  ] ;
    Fallunterscheidung (
      IstLeer ( a ) oder IstLeer ( s ) oder n = 0 ; "" ;
      Wiederhole ( " " ; ma * 2 ) & Wiederhole ( "█" ; md * 2 )
    )
)
```

---

## F-14 bis F-16 · Pixelwerte — nur für die grafische Zeitachse

Für die Zeichenketten-Zeitachse **nicht nötig**. Wer die einfache Variante nimmt,
überspringt sie und spart auch die Beziehung B-22 und das Feld
`ZeitachseBreitePx`.

**F-14 · `Abschnitte::c_BandStartPx` / `c_BandBreitePx`**
```
Setze ( [ n = Abschnitte_Projekte::BogenLaengeMonate ;
          w = Abschnitte_Einstellungen::ZeitachseBreitePx ] ;
  Fallunterscheidung ( n = 0 ; 0 ;
    Runden ( ( Abschnitte::Band_Von_Monat - 1 ) / n * w ; 0 ) ) )
```
```
Setze ( [ n = Abschnitte_Projekte::BogenLaengeMonate ;
          w = Abschnitte_Einstellungen::ZeitachseBreitePx ] ;
  Fallunterscheidung ( n = 0 ; 0 ;
    Max ( 4 ; Runden ( ( Abschnitte::Band_Bis_Monat - Abschnitte::Band_Von_Monat + 1 ) / n * w ; 0 ) ) ) )
```

**F-15 · `c_IstStartPx` / `c_IstBreitePx`** — analog mit `Ist_Start` und `Ist_Ende`.

**F-16 · `Projekte::c_HeutePx`**
```
Setze ( [ m = Projekte::c_MonatImProjekt ;
          n = Projekte::BogenLaengeMonate ;
          w = Projekte_Einstellungen::ZeitachseBreitePx ] ;
  Fallunterscheidung ( n = 0 ; 0 ; Runden ( ( m - 1 ) / n * w ; 0 ) ) )
```

---

## F-17 · `Personen` — zwei Textfelder, nicht gespeichert

`c_Name`: `Personen::Vorname & " " & Personen::Nachname`

`c_Kurzname`: `Links ( Personen::Vorname ; 1 ) & ". " & Personen::Nachname`

## F-18 · `Aufgaben::c_IstOffen` — Zahl, **gespeichert**

```
Wenn ( Aufgaben::Status = "erledigt" ; 0 ; 1 )
```

## F-19 · `Projekte::k_Eins` — Zahl, **gespeichert**

```
1
```

Konstante für kartesische Bezüge und Portalfilter.

---

## F-20 · Portalfilter

Einzutragen unter *Portal einrichten ▸ Portaldatensätze filtern*. Diese sieben
Formeln ersetzen sieben zusätzliche Tabellenauftreten.

**F-20.1 · Hinweise der eigenen Stufe** (Bildschirme 01, 02)
```
Hinweise::Stufe = Projekte::g_MeineStufe
und Hinweise::Weggeklickt = 0
und Hinweise::Zurueckgezogen = 0
```

**F-20.2 · Meine offenen Aufgaben** (Bildschirm 01)
```
Aufgaben::_fkPersonID = Projekte::g_MeinePersonID
und Aufgaben::Status ≠ "erledigt"
```

**F-20.3 · Nur der laufende Abschnitt** (Bildschirm 01)
```
Abschnitte::Status = "läuft"
```

**F-20.4 · Nur zutreffende Schritte** (Bildschirme 01, 04)
```
Schritte::TrifftNichtZu = 0
```

**F-20.5 · Checkliste des laufenden Abschnitts** (Bildschirm 01)
```
Punkte::TrifftNichtZu = 0
und Punkte::_fkAbschnittID = Projekte::s_AktuelleAbschnittID
```

**F-20.6 · Überfällige Aufgaben** (Bildschirm 02)
```
Aufgaben::Termin < Hole ( AktuellesDatum )
und Aufgaben::Status ≠ "erledigt"
```

**F-20.7 · Noch nicht freigegebene Ergebnisse** (Bildschirm 04)
```
Ergebnisse::Status ≠ "freigegeben"
```

---

## F-21 · Bedingte Formatierung

Einzutragen unter *Format ▸ Bedingt*. Gedämpfte Farben — der Leitgedanke verbietet
Alarmoptik.

| Objekt | Bedingung | Formatierung |
|---|---|---|
| Zustandstext, Bildschirm 02 | `Projekte::s_Zustand = "stockt"` | Textfarbe `#B8860B` (gedämpftes Gold) |
| Zustandstext | `Projekte::s_Zustand = "ruht"` | Textfarbe `#9A9A9A` |
| Zustandstext | `Projekte::s_Zustand = "abgeschlossen"` | Textfarbe `#4A7C59` |
| Checklistenzeile | `Punkte::IstUeberschrift = 1` | Fett, Hintergrund `#F2F2F2` |
| Checklistenzeile | `Punkte::Erledigt = 1` | Textfarbe `#8A8A8A` |
| Checklistenzeile | `Punkte::TrifftNichtZu = 1` | Textfarbe `#C0C0C0`, durchgestrichen |
| Abschnittszeile, Bildschirm 03 | `Abschnitte::Status = "läuft"` | Fett |
| Kasten „Worauf zu schauen ist" | `Projekte::g_MeineStufe ≠ "Leitung"` | *Objekt ▸ Ausblenden wenn* — nicht bedingte Formatierung |

**Nirgends Rot.** Verzug erscheint als verschobenes Datum, nicht als Warnfarbe.

---

## F-24 · Zugriffsformeln der Rechtesets

Diese beiden Formeln stehen **nicht** in Feldern, sondern im Rechteset unter
*Datensätze ▸ Angepasste Zugriffsrechte*. Vollständige Anleitung in
[`07-rechte-und-mehrbenutzer.md`](07-rechte-und-mehrbenutzer.md).

**F-24.1 · Tabelle `Projekte`, Rechteset `Forschende`**
```
nicht IstLeer ( FilterWerte ( Projekte::s_BeteiligtIDs ; Projekte::g_MeinePersonID ) )
```

**F-24.2 · Alle übrigen Datentabellen, Rechteset `Forschende`**
```
nicht IstLeer ( FilterWerte ( <Tabelle>::s_ZugriffIDs ; Projekte::g_MeinePersonID ) )
```

`FilterWerte` vergleicht zwei zeilenweise Listen und liefert die Schnittmenge.
Ist die leer, gehört die Person nicht zum Projekt und sieht den Datensatz nicht.

> Beide Formeln lesen **nur lokale Felder plus ein globales Feld**. Das ist
> Absicht: Zugriffsformeln werden für jeden Datensatz einzeln ausgewertet.
> Ein `AuswerteSQL` oder ein Bezug an dieser Stelle wäre bei jeder Suche spürbar
> langsam und im Verhalten schwer vorhersagbar.
