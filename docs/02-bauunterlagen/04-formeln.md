# Formeln

Alle Berechnungen zum Einfügen. **Kontext** ist das Tabellenauftreten, das im
Formeleditor oben links eingestellt sein muss — bei falschem Kontext findet
FileMaker die Bezugsfelder nicht.

## Vorbemerkung: Funktionsnamen und Sprache

Die Formeln stehen mit **englischen Funktionsnamen**. FileMaker zeigt
Funktionsnamen in der Sprache der Programmoberfläche an; in einer deutschen
Installation lassen sich englische Namen nicht einfach einfügen.

Zwei Wege:

- **Empfohlen:** FileMaker Pro auf Englisch stellen. Der Aufbau der Formeln
  bleibt identisch, nur die Namen sind vertraut aus jeder Dokumentation.
- Andernfalls: die Struktur übernehmen und jede Funktion über die Funktionsliste
  rechts im Formeleditor einsetzen — dort erscheinen die deutschen Namen. Die
  Operatoren (`&`, `=`, `≤`, `;`) sind in beiden Sprachen gleich.

Benutzte Funktionen: `Let` `Case` `If` `IsEmpty` `Get` `Year` `Month` `Day`
`Date` `Max` `Min` `Round` `Sum` `GetAsNumber` `GetAsDate` `Right` `Left`
`MonthName` `ExecuteSQL`.

---

## F-01 · `Projects::c_Projektcode` — Text, **nicht** gespeichert

```
Year ( GetAsDate ( Projects::ErstelltAm ) ) & "-" & Right ( "000" & Projects::LfdNr ; 3 )
```

Ergibt `2026-014`.

---

## F-02 · `Projects::c_MonatImProjekt` — Zahl, nicht gespeichert

Der Grundwert des ganzen Systems. Monat 1 ist der Startmonat.

```
Let ( [
    s = Projects::Startdatum ;
    h = Get ( CurrentDate )
  ] ;
    Case (
      IsEmpty ( s ) ; "" ;
      ( Year ( h ) - Year ( s ) ) * 12
      + ( Month ( h ) - Month ( s ) )
      + If ( Day ( h ) ≥ Day ( s ) ; 1 ; 0 )
    )
)
```

Start 15.01., heute 20.01. → Monat 1. Heute 14.02. → immer noch Monat 1.
Heute 15.02. → Monat 2.

---

## F-03 · `Projects::c_SollAbschnittNr` — Zahl, nicht gespeichert

In welchem Abschnitt man planmäßig sein *sollte*. Nutzt die überschneidungsfreien
`Schwerpunkt`-Bereiche — mit den überlappenden `Band`-Bereichen wäre die Antwort
mehrdeutig.

```
Let ( [
    m = Projects::c_MonatImProjekt ;
    r = ExecuteSQL (
          "SELECT \"Nr\" FROM \"Sections\"
             WHERE \"_fkProjectID\" = ?
               AND \"Schwerpunkt_Von_Monat\" <= ?
               AND \"Schwerpunkt_Bis_Monat\" >= ?" ;
          "" ; "" ;
          Projects::__pkProjectID ; m ; m )
  ] ;
    Case ( IsEmpty ( r ) ; "" ; GetAsNumber ( r ) )
)
```

> `ExecuteSQL` erspart hier ein gefiltertes Tabellenauftreten. Es ist nicht
> gespeichert und in Listen zu langsam — deshalb schreibt Skript S-08 den Wert
> nachts nach `s_AktuellerAbschnittNr`, und Listen lesen nur dort.

## F-04 · `Projects::c_SollAbschnittName` — Text, nicht gespeichert

```
Let ( [
    m = Projects::c_MonatImProjekt ;
    r = ExecuteSQL (
          "SELECT \"Name\" FROM \"Sections\"
             WHERE \"_fkProjectID\" = ?
               AND \"Schwerpunkt_Von_Monat\" <= ?
               AND \"Schwerpunkt_Bis_Monat\" >= ?" ;
          "" ; "" ;
          Projects::__pkProjectID ; m ; m )
  ] ;
    Substitute ( r ; ¶ ; "" )
)
```

---

## F-05 · `Projects::c_Standortsatz` — Text, nicht gespeichert

**Der wichtigste Einzelwert im System.** Steht ganz oben auf jeder Projektakte und
beantwortet die Frage, die sich Unerfahrene nicht zu stellen trauen.

```
Let ( [
    m     = Projects::c_MonatImProjekt ;
    n     = Projects::BogenLaengeMonate ;
    istN  = Projects::s_AktuellerAbschnittNr ;
    istT  = Projects::s_AktuellerAbschnittName ;
    sollN = Projects::c_SollAbschnittNr ;
    sollT = Projects::c_SollAbschnittName
  ] ;
    Case (
      IsEmpty ( Projects::Startdatum ) ;
        "Kein Startdatum hinterlegt — ohne das kann das System nicht sagen, wo Sie stehen." ;

      m > n ;
        "Monat " & m & ". Der geplante Bogen von " & n & " Monaten ist abgelaufen. Das kommt häufig vor und ist kein Grund zur Sorge." ;

      IsEmpty ( istN ) ;
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

## F-06 · `Projects::c_ZeitfortschrittProzent` — Zahl, nicht gespeichert

```
Let ( [
    m = Projects::c_MonatImProjekt ;
    n = Projects::BogenLaengeMonate
  ] ;
    Case (
      IsEmpty ( m ) or n = 0 ; 0 ;
      Min ( 100 ; Round ( m / n * 100 ; 0 ) )
    )
)
```

Im Layout als Balken darstellen: Feld ▸ *Steuerungsstil* ▸ **Balkendiagramm**,
Bereich 0 bis 100.

---

## F-07 · `Projects::c_Verzug` — Zahl, nicht gespeichert

Wie viele Monate hinter dem Plan. Basis der Fortschreibung.

```
Let ( [
    m   = Projects::c_MonatImProjekt ;
    bis = Projects::s_AktuellerAbschnittSchwerpunktBis
  ] ;
    Case (
      IsEmpty ( bis ) ; 0 ;
      Max ( 0 ; m - bis )
    )
)
```

Nur nach oben — wer schneller ist, bekommt keinen negativen Verzug
angerechnet. Die Einreichung rückt dadurch nie nach vorne, was der Erfahrung
entspricht.

## F-08 · `Projects::c_EinreichungVoraussichtlich` — Datum, nicht gespeichert

```
Let ( [
    s = Projects::Startdatum ;
    z = Projects::ZielpunktEinreichungMonat ;
    v = Projects::c_Verzug
  ] ;
    If ( IsEmpty ( s ) ; "" ;
      Date ( Month ( s ) + z + v ; Day ( s ) ; Year ( s ) )
    )
)
```

`Date()` rechnet Monatsüberläufe selbst um — Monat 27 wird zu März des Folgejahres.

## F-09 · `Projects::c_EinreichungText` — Text, nicht gespeichert

```
Let ( d = Projects::c_EinreichungVoraussichtlich ;
    If ( IsEmpty ( d ) ; "—" ; MonthName ( d ) & " " & Year ( d ) )
)
```

Ergibt `März 2028`. Bewusst ohne Tagesangabe: eine Prognose auf den Tag genau
wäre eine Genauigkeit, die es nicht gibt.

---

## F-10 · `Projects::c_Zustand` — Text, nicht gespeichert

```
Let ( [
    st     = Projects::Status ;
    letzte = Projects::s_LetzteAktivitaet ;
    tage   = Get ( CurrentDate ) - letzte ;
    f1     = Projects_zz_Utility::StilleFristTage ;
    f2     = Projects_zz_Utility::RuhtFristTage
  ] ;
    Case (
      st = "abgeschlossen" ; "abgeschlossen" ;
      st = "abgebrochen"   ; "abgebrochen" ;
      st = "ruht"          ; "ruht" ;
      IsEmpty ( letzte )   ; "läuft" ;
      tage > f2            ; "ruht" ;
      tage > f1            ; "stockt" ;
                             "läuft"
    )
)
```

---

## F-11 · `ChecklistItems` — zwei gespeicherte Zahlenfelder

**Wichtig: bei beiden den Haken bei *Ergebnis nicht speichern* entfernen.** Nur
gespeicherte Felder lassen sich schnell summieren.

`IstOffen`:
```
If (
  ChecklistItems::Erledigt = 1
  or ChecklistItems::TrifftNichtZu = 1
  or ChecklistItems::IstUeberschrift = 1
  ; 0 ; 1 )
```

`IstGezaehlt`:
```
If (
  ChecklistItems::TrifftNichtZu = 1
  or ChecklistItems::IstUeberschrift = 1
  ; 0 ; 1 )
```

> **Was ist `IstUeberschrift`?** FileMaker kann keine Portale ineinander
> verschachteln. Damit die Checkliste trotzdem nach Schritten gegliedert aussieht,
> legt Skript S-03 je Schritt einen zusätzlichen Datensatz mit `Nr = 0`,
> `IstUeberschrift = 1` und dem Schrittnamen in `Punkt` an. Sortiert nach
> `StepNr, Nr` landet er automatisch als Überschrift über seiner Gruppe. Im Layout
> wird bei diesen Zeilen das Kontrollkästchen ausgeblendet und der Text fett
> gesetzt. Ein Feld und drei Skriptzeilen ersetzen so das fehlende
> Verschachtelungs-Feature.

---

## F-12 · `c_PunkteGesamt` — Zahl, nicht gespeichert

Kontext **Steps**:
```
Sum ( Steps_ChecklistItems::IstGezaehlt )
```

Kontext **Sections**:
```
Sum ( Sections_ChecklistItems::IstGezaehlt )
```

## F-13 · `c_PunkteErledigt` — Zahl, nicht gespeichert

Kontext **Steps**:
```
Sum ( Steps_ChecklistItems::IstGezaehlt ) - Sum ( Steps_ChecklistItems::IstOffen )
```

Kontext **Sections**:
```
Sum ( Sections_ChecklistItems::IstGezaehlt ) - Sum ( Sections_ChecklistItems::IstOffen )
```

Anzeige „3/9" im Layout als Zusammenfassungstext:
`c_PunkteErledigt & "/" & c_PunkteGesamt`

---

## CF-01 · Custom Function `Wiederhole ( text ; anzahl )`

*Datei ▸ Verwalten ▸ Benutzerdefinierte Funktionen ▸ Neu*

```
Case ( anzahl < 1 ; "" ; text & Wiederhole ( text ; anzahl - 1 ) )
```

Rekursiv, für die Zeitachse. FileMaker hat keine eingebaute Wiederholfunktion;
diese vier Wörter ersparen jede Bastelei mit `Substitute ( 10^n - 1 ; … )`.

## F-22 · `Sections::c_BandGrafik` — Text, nicht gespeichert

Der **geplante** Balken der Zeitachse als Zeichenkette. Zwei Zeichen je Monat.
Im Layout in einer nichtproportionalen Schrift darstellen (Doc 06 §5).

```
Let ( [
    n  = Sections_Projects::BogenLaengeMonate ;
    bv = Sections::Band_Von_Monat ;
    bb = Sections::Band_Bis_Monat
  ] ;
    Case ( n = 0 ; "" ;
      Wiederhole ( "·" ; ( bv - 1 ) * 2 )
    & Wiederhole ( "▓" ; ( bb - bv + 1 ) * 2 )
    & Wiederhole ( "·" ; ( n - bb ) * 2 )
    )
)
```

## F-23 · `Sections::c_IstGrafik` — Text, nicht gespeichert

Der **tatsächliche** Balken. Läuft der Abschnitt noch, endet er bei heute.

```
Let ( [
    s  = Sections_Projects::Startdatum ;
    a  = Sections::Ist_Start ;
    e  = If ( IsEmpty ( Sections::Ist_Ende ) ; Get ( CurrentDate ) ; Sections::Ist_Ende ) ;
    n  = Sections_Projects::BogenLaengeMonate ;
    ma = ( Year ( a ) - Year ( s ) ) * 12 + ( Month ( a ) - Month ( s ) ) ;
    md = ( Year ( e ) - Year ( a ) ) * 12 + ( Month ( e ) - Month ( a ) ) + 1
  ] ;
    Case (
      IsEmpty ( a ) or IsEmpty ( s ) or n = 0 ; "" ;
      Wiederhole ( " " ; ma * 2 ) & Wiederhole ( "█" ; md * 2 )
    )
)
```

---

## F-14 bis F-16 · Pixelwerte — nur für die grafische Zeitachse

Diese drei Formeln werden für die **Zeichenketten-Zeitachse nicht gebraucht**.
Sie sind vorbereitet, falls die Zeitachse später mit echten Rechtecken gebaut
werden soll (Doc 06 §5, Kasten). Wer die einfache Variante nimmt, überspringt
sie — und spart auch die Beziehung B-22 und das Feld `ZeitachseBreitePx`.

## F-14 · Zeitachse, Soll-Balken — Kontext `Sections`, Zahl, nicht gespeichert

`c_BandStartPx`:
```
Let ( [
    n = Sections_Projects::BogenLaengeMonate ;
    w = Sections_zz_Utility::ZeitachseBreitePx
  ] ;
    Case ( n = 0 ; 0 ;
      Round ( ( Sections::Band_Von_Monat - 1 ) / n * w ; 0 ) )
)
```

`c_BandBreitePx`:
```
Let ( [
    n = Sections_Projects::BogenLaengeMonate ;
    w = Sections_zz_Utility::ZeitachseBreitePx
  ] ;
    Case ( n = 0 ; 0 ;
      Max ( 4 ; Round ( ( Sections::Band_Bis_Monat - Sections::Band_Von_Monat + 1 ) / n * w ; 0 ) ) )
)
```

## F-15 · Zeitachse, Ist-Balken — Kontext `Sections`

`c_IstStartPx`:
```
Let ( [
    s = Sections_Projects::Startdatum ;
    a = Sections::Ist_Start ;
    n = Sections_Projects::BogenLaengeMonate ;
    w = Sections_zz_Utility::ZeitachseBreitePx ;
    ma = ( Year ( a ) - Year ( s ) ) * 12 + ( Month ( a ) - Month ( s ) )
  ] ;
    Case (
      IsEmpty ( a ) or IsEmpty ( s ) or n = 0 ; 0 ;
      Round ( ma / n * w ; 0 )
    )
)
```

`c_IstBreitePx`:
```
Let ( [
    s = Sections_Projects::Startdatum ;
    a = Sections::Ist_Start ;
    e = If ( IsEmpty ( Sections::Ist_Ende ) ; Get ( CurrentDate ) ; Sections::Ist_Ende ) ;
    n = Sections_Projects::BogenLaengeMonate ;
    w = Sections_zz_Utility::ZeitachseBreitePx ;
    dauer = ( Year ( e ) - Year ( a ) ) * 12 + ( Month ( e ) - Month ( a ) ) + 1
  ] ;
    Case (
      IsEmpty ( a ) or IsEmpty ( s ) or n = 0 ; 0 ;
      Max ( 4 ; Round ( dauer / n * w ; 0 ) )
    )
)
```

## F-16 · `Projects::c_HeutePx` — Zahl, nicht gespeichert

```
Let ( [
    m = Projects::c_MonatImProjekt ;
    n = Projects::BogenLaengeMonate ;
    w = Projects_zz_Utility::ZeitachseBreitePx
  ] ;
    Case ( n = 0 ; 0 ; Round ( ( m - 1 ) / n * w ; 0 ) )
)
```

---

## F-17 · `People` — zwei Textfelder, nicht gespeichert

`c_Name`: `People::Vorname & " " & People::Nachname`

`c_Kurzname`: `Left ( People::Vorname ; 1 ) & ". " & People::Nachname`

## F-18 · `Tasks::c_IstOffen` — Zahl, **gespeichert**

```
If ( Tasks::Status = "erledigt" ; 0 ; 1 )
```

## F-19 · `Projects::k_Eins` — Zahl, **gespeichert**

```
1
```

Konstante für kartesische Bezüge und Portalfilter. Wirkt sinnlos, erspart aber
mehrere Hilfsfelder.

---

## F-20 · Portalfilter

Einzutragen unter *Portal einrichten ▸ Portaldatensätze filtern*. Diese sieben
Formeln ersetzen sieben zusätzliche Tabellenauftreten.

**F-20.1 · Hinweise der eigenen Stufe** (Bildschirme 01, 02)
```
Nudges::Stufe = Projects::g_MeineStufe
and Nudges::Weggeklickt = 0
and Nudges::Zurueckgezogen = 0
```

**F-20.2 · Meine offenen Aufgaben** (Bildschirm 01)
```
Tasks::_fkPersonID = Projects::g_MeinePersonID
and Tasks::Status ≠ "erledigt"
```

**F-20.3 · Nur der laufende Abschnitt** (Bildschirm 01)
```
Sections::Status = "läuft"
```

**F-20.4 · Nur zutreffende Schritte** (Bildschirme 01, 04)
```
Steps::TrifftNichtZu = 0
```

**F-20.5 · Checkliste des laufenden Abschnitts** (Bildschirm 01)
```
ChecklistItems::TrifftNichtZu = 0
and ChecklistItems::_fkSectionID = Projects::s_AktuelleSectionID
```

**F-20.6 · Überfällige Aufgaben** (Bildschirm 02)
```
Tasks::Termin < Get ( CurrentDate )
and Tasks::Status ≠ "erledigt"
```

**F-20.7 · Offene Ergebnisse eines Abschnitts** (Bildschirm 04)
```
Deliverables::Status ≠ "freigegeben"
```

> F-20.5 braucht das zusätzliche Feld `Projects::s_AktuelleSectionID` (Text),
> gesetzt von Skript S-04. Es ist der einzige Wert, den Bildschirm 01 nicht aus
> den bereits vorhandenen Feldern ableiten kann.

---

## F-21 · Bedingte Formatierung

Einzutragen unter *Format ▸ Bedingt*. Gedämpfte Farben — der Leitgedanke verbietet
Alarmoptik.

| Objekt | Bedingung | Formatierung |
|---|---|---|
| Zustandstext, Bildschirm 02 | `Projects::s_Zustand = "stockt"` | Textfarbe `#B8860B` (gedämpftes Gold) |
| Zustandstext | `Projects::s_Zustand = "ruht"` | Textfarbe `#9A9A9A` |
| Zustandstext | `Projects::s_Zustand = "abgeschlossen"` | Textfarbe `#4A7C59` |
| Checklistenzeile | `ChecklistItems::IstUeberschrift = 1` | Fett, Hintergrund `#F2F2F2`, Kontrollkästchen ausblenden |
| Checklistenzeile | `ChecklistItems::Erledigt = 1` | Textfarbe `#8A8A8A` |
| Checklistenzeile | `ChecklistItems::TrifftNichtZu = 1` | Textfarbe `#C0C0C0`, durchgestrichen |
| Abschnittszeile, Bildschirm 03 | `Sections::Status = "läuft"` | Fett |
| Kasten „Worauf zu schauen ist" | `Projects::g_MeineStufe ≠ "Leitung"` | *Objekt ▸ Ausblenden wenn* — nicht bedingte Formatierung |

**Nirgends Rot.** Verzug erscheint als verschobenes Datum, nicht als Warnfarbe.
