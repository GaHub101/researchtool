# Skripte

**Neun Skripte.** Das ist der einzige Posten, der sich nicht importieren lässt —
FileMaker-Skripte müssen von Hand zusammengeklickt werden. Deshalb sind sie so
wenige und so kurz wie möglich gehalten: S-09 wird von fast allen anderen
aufgerufen, statt dass jedes Skript sein eigenes Protokoll schreibt.

## Vor dem Anfangen

**Skriptschritte sind lokalisiert.** Unten stehen die englischen Bezeichnungen.
Die häufigsten deutschen Entsprechungen:

| Englisch | Deutsch |
|---|---|
| Set Variable | Variable setzen |
| Set Field | Feldwert setzen |
| Go to Layout | Gehe zu Layout |
| Go to Related Record | Gehe zu Bezugsdatensatz |
| New Record/Request | Neuer Datensatz/Abfrage |
| Go to Portal Row | Gehe zu Portalzeile |
| Loop / Exit Loop If / End Loop | Schleife / Verlasse Schleife wenn / Ende Schleife |
| Perform Find | Suchen |
| Show Custom Dialog | Eigenes Dialogfeld anzeigen |
| Commit Records | Schreibe Änderung Datens./Abfrage |
| Freeze Window | Fenster fixieren |
| If / Else If / End If | Wenn / Sonst wenn / Ende (wenn) |

**Alle Skripte** beginnen mit `Freeze Window` und laufen mit
*Fehleraufzeichnung ein* (`Set Error Capture [ On ]`), damit ein Abbruch nicht als
FileMaker-Fehlermeldung beim Anwender landet.

**Hilfslayouts:** Skripte, die Datensätze in einer anderen Tabelle anlegen,
brauchen dort einen Kontext. Dafür gibt es die Layouts `90`–`98` (Doc 06 §8) —
leere Listenlayouts ohne Objekte, nur zum Wechseln.

---

## S-01 · Start

*Auslöser: Datei ▸ Verwalten ▸ Skripte ▸ Skriptauslöser ▸ BeimÖffnen*

Setzt die drei globalen Felder, von denen alle Portalfilter und Sichtbarkeiten
abhängen, und schickt in die richtige Einstiegsseite.

```
 1  Set Error Capture [ On ]
 2  Freeze Window
 3  Set Variable [ $konto ; Get ( AccountName ) ]
 4  Set Variable [ $set   ; Get ( AccountPrivilegeSetName ) ]
 5  Go to Layout [ "90 Projects_Utility" ]
 6  Set Field [ Projects::g_MeinKonto ; $konto ]
 7  Set Field [ Projects::g_MeineStufe ;
       If ( $set = "Forschungsleitung" ; "Leitung" ; "Forschende" ) ]
 8  # Person zum Konto suchen
 9  Set Variable [ $personID ; ExecuteSQL (
       "SELECT \"__pkPersonID\" FROM \"People\" WHERE \"Kontoname\" = ?" ;
       "" ; "" ; $konto ) ]
10  Set Field [ Projects::g_MeinePersonID ; $personID ]
11  If [ Projects::g_MeineStufe = "Leitung" ]
12      Perform Script [ "S-02 Meine Projekte laden" ]
13  Else
14      Perform Script [ "S-02 Meine Projekte laden" ]
15      Go to Layout [ "01 Mein Projekt" ]
16  End If
```

> Zeile 7 ist die einzige Stelle, an der die Stufe technisch entschieden wird.
> Ändert sich einmal die Benennung der Rechtesets, ist hier der eine Ort dafür.

---

## S-02 · Meine Projekte laden

Sucht die Projekte, an denen die angemeldete Person beteiligt ist, und
entscheidet, wo sie landet.

```
 1  Set Error Capture [ On ]
 2  Freeze Window
 3  Set Variable [ $ids ; ExecuteSQL (
       "SELECT DISTINCT \"_fkProjectID\" FROM \"ProjectPeople\" WHERE \"_fkPersonID\" = ?" ;
       "" ; "" ; Projects::g_MeinePersonID ) ]
 4  Go to Layout [ "90 Projects_Utility" ]
 5  If [ IsEmpty ( $ids ) and Projects::g_MeineStufe = "Leitung" ]
 6      Show All Records                       # Leitung ohne Zuordnung sieht alles
 7  Else If [ IsEmpty ( $ids ) ]
 8      Show Custom Dialog [ "Noch kein Projekt" ;
          "Ihnen ist noch kein Projekt zugeordnet. Legen Sie eins an oder wenden Sie sich an die Betreuung." ]
 9      Go to Layout [ "06 Projektauswahl" ]
10      Exit Script
11  Else
12      Enter Find Mode [ Pause: Off ]
13      Set Field [ Projects::__pkProjectID ;
          Substitute ( $ids ; ¶ ; ¶ ) ]        # mehrere Zeilen = ODER-Suche
14      Perform Find
15  End If
16  Sort Records [ nach Projects::Titel, aufsteigend ; ohne Dialog ]
17  If [ Projects::g_MeineStufe = "Leitung" ]
18      Go to Layout [ "02 Meine Projekte" ]
19  Else If [ Get ( FoundCount ) = 1 ]
20      Go to Layout [ "01 Mein Projekt" ]
21  Else
22      Go to Layout [ "06 Projektauswahl" ]
23  End If
```

> **Zeile 13 ist ein Trick:** Im Suchmodus erzeugt jede Zeile eines mehrzeiligen
> Werts eine eigene Suchabfrage — das ergibt eine ODER-Verknüpfung ohne Schleife.
> Das `Substitute` ist reine Absicherung gegen Leerzeilen.

---

## S-03 · Projekt anlegen

**Das Herzstück.** Erzeugt aus Titel und Startdatum den kompletten
24-Monats-Bogen: 5 Abschnitte, 13 Schritte, 59 Checklistenpunkte plus 13
Überschriftszeilen — mit allen Texten der Wissensschicht.

```
 1  Set Error Capture [ On ]
 2  Freeze Window
 3  Show Custom Dialog [ "Neues Projekt" ; "Titel:" ; Projects::g_Eingabe1 ]
 4  If [ Get ( LastMessageChoice ) = 2 ] Exit Script  End If
 5
 6  # ---- Projekt anlegen ----
 7  Go to Layout [ "90 Projects_Utility" ]
 8  New Record/Request
 9  Set Field [ Projects::Titel ; Projects::g_Eingabe1 ]
10  Set Field [ Projects::Startdatum ; Get ( CurrentDate ) ]
11  Commit Records [ ohne Dialog ]
12  Set Variable [ $projektID ; Projects::__pkProjectID ]
13  Set Variable [ $start     ; Projects::Startdatum ]
14
15  # ---- Schleife über die Abschnittsvorlagen ----
16  Go to Layout [ "94 SectionTemplates_Utility" ]
17  Show All Records
18  Sort Records [ nach SectionTemplates::Nr ; ohne Dialog ]
19  Go to Record [ Erster ]
20  Loop
21      Set Variable [ $s ; JSONSetElement ( "{}" ;
            [ "nr"      ; SectionTemplates::Nr                    ; JSONNumber ] ;
            [ "name"    ; SectionTemplates::Name                  ; JSONString ] ;
            [ "leitfr"  ; SectionTemplates::Leitfrage             ; JSONString ] ;
            [ "bv"      ; SectionTemplates::Band_Von_Monat        ; JSONNumber ] ;
            [ "bb"      ; SectionTemplates::Band_Bis_Monat        ; JSONNumber ] ;
            [ "sv"      ; SectionTemplates::Schwerpunkt_Von_Monat ; JSONNumber ] ;
            [ "sb"      ; SectionTemplates::Schwerpunkt_Bis_Monat ; JSONNumber ] ;
            [ "zweck"   ; SectionTemplates::Zweck                 ; JSONString ] ;
            [ "stolp"   ; SectionTemplates::Stolpersteine         ; JSONString ] ;
            [ "hf"      ; SectionTemplates::Hinweis_Forschende    ; JSONString ] ;
            [ "hl"      ; SectionTemplates::Hinweis_Leitung       ; JSONString ] ) ]
22      Set Variable [ $sectionNr ; SectionTemplates::Nr ]
23
24      Go to Layout [ "91 Sections_Utility" ]
25      New Record/Request
26      Set Field [ Sections::_fkProjectID ; $projektID ]
27      Set Field [ Sections::Nr        ; JSONGetElement ( $s ; "nr" ) ]
28      Set Field [ Sections::Name      ; JSONGetElement ( $s ; "name" ) ]
29      Set Field [ Sections::Leitfrage ; JSONGetElement ( $s ; "leitfr" ) ]
30      Set Field [ Sections::Band_Von_Monat        ; JSONGetElement ( $s ; "bv" ) ]
31      Set Field [ Sections::Band_Bis_Monat        ; JSONGetElement ( $s ; "bb" ) ]
32      Set Field [ Sections::Schwerpunkt_Von_Monat ; JSONGetElement ( $s ; "sv" ) ]
33      Set Field [ Sections::Schwerpunkt_Bis_Monat ; JSONGetElement ( $s ; "sb" ) ]
34      Set Field [ Sections::Zweck              ; JSONGetElement ( $s ; "zweck" ) ]
35      Set Field [ Sections::Stolpersteine      ; JSONGetElement ( $s ; "stolp" ) ]
36      Set Field [ Sections::Hinweis_Forschende ; JSONGetElement ( $s ; "hf" ) ]
37      Set Field [ Sections::Hinweis_Leitung    ; JSONGetElement ( $s ; "hl" ) ]
38      # Solltermine aus dem Startdatum
39      Set Field [ Sections::Soll_Start ;
            Date ( Month ( $start ) + JSONGetElement ( $s ; "bv" ) - 1 ; Day ( $start ) ; Year ( $start ) ) ]
40      Set Field [ Sections::Soll_Ende ;
            Date ( Month ( $start ) + JSONGetElement ( $s ; "bb" ) ; Day ( $start ) - 1 ; Year ( $start ) ) ]
41      Set Field [ Sections::Status ; "offen" ]
42      Commit Records [ ohne Dialog ]
43      Set Variable [ $sectionID ; Sections::__pkSectionID ]
44
45      # ---- Schleife über die Schrittvorlagen dieses Abschnitts ----
46      Go to Layout [ "95 StepTemplates_Utility" ]
47      Enter Find Mode [ ] · Set Field [ StepTemplates::SectionNr ; $sectionNr ] · Perform Find
48      Sort Records [ nach StepTemplates::Nr ; ohne Dialog ]
49      Go to Record [ Erster ]
50      Loop
51          Set Variable [ $t ; JSONSetElement ( "{}" ;
                [ "nr"    ; StepTemplates::Nr                  ; JSONNumber ] ;
                [ "name"  ; StepTemplates::Name                ; JSONString ] ;
                [ "zweck" ; StepTemplates::Zweck               ; JSONString ] ;
                [ "stolp" ; StepTemplates::Stolpersteine       ; JSONString ] ;
                [ "hf"    ; StepTemplates::Hinweis_Forschende  ; JSONString ] ;
                [ "hl"    ; StepTemplates::Hinweis_Leitung     ; JSONString ] ) ]
52          Set Variable [ $stepNr ; StepTemplates::Nr ]
53
54          Go to Layout [ "92 Steps_Utility" ]
55          New Record/Request
56          Set Field [ Steps::_fkSectionID ; $sectionID ]
57          Set Field [ Steps::_fkProjectID ; $projektID ]
58          Set Field [ Steps::Nr    ; JSONGetElement ( $t ; "nr" ) ]
59          Set Field [ Steps::Name  ; JSONGetElement ( $t ; "name" ) ]
60          Set Field [ Steps::Zweck ; JSONGetElement ( $t ; "zweck" ) ]
61          Set Field [ Steps::Stolpersteine      ; JSONGetElement ( $t ; "stolp" ) ]
62          Set Field [ Steps::Hinweis_Forschende ; JSONGetElement ( $t ; "hf" ) ]
63          Set Field [ Steps::Hinweis_Leitung    ; JSONGetElement ( $t ; "hl" ) ]
64          Set Field [ Steps::Status ; "offen" ]
65          Commit Records [ ohne Dialog ]
66          Set Variable [ $stepID   ; Steps::__pkStepID ]
67          Set Variable [ $stepName ; Steps::Name ]
68
69          # ---- Überschriftszeile (Ersatz für verschachtelte Portale) ----
70          Go to Layout [ "93 ChecklistItems_Utility" ]
71          New Record/Request
72          Set Field [ ChecklistItems::_fkStepID    ; $stepID ]
73          Set Field [ ChecklistItems::_fkSectionID ; $sectionID ]
74          Set Field [ ChecklistItems::_fkProjectID ; $projektID ]
75          Set Field [ ChecklistItems::StepNr ; $stepNr ]
76          Set Field [ ChecklistItems::Nr     ; 0 ]
77          Set Field [ ChecklistItems::Punkt  ; $stepName ]
78          Set Field [ ChecklistItems::IstUeberschrift ; 1 ]
79          Commit Records [ ohne Dialog ]
80
81          # ---- Schleife über die Checklistenvorlagen ----
82          Go to Layout [ "96 ChecklistTemplates_Utility" ]
83          Enter Find Mode [ ]
84          Set Field [ ChecklistTemplates::SectionNr ; $sectionNr ]
85          Set Field [ ChecklistTemplates::StepNr    ; $stepNr ]
86          Perform Find
87          Sort Records [ nach ChecklistTemplates::Nr ; ohne Dialog ]
88          If [ Get ( FoundCount ) > 0 ]
89              Go to Record [ Erster ]
90              Loop
91                  Set Variable [ $p ; ChecklistTemplates::Punkt ]
92                  Set Variable [ $e ; ChecklistTemplates::Erklaerung ]
93                  Set Variable [ $n ; ChecklistTemplates::Nr ]
94                  Go to Layout [ "93 ChecklistItems_Utility" ]
95                  New Record/Request
96                  Set Field [ ChecklistItems::_fkStepID    ; $stepID ]
97                  Set Field [ ChecklistItems::_fkSectionID ; $sectionID ]
98                  Set Field [ ChecklistItems::_fkProjectID ; $projektID ]
99                  Set Field [ ChecklistItems::StepNr     ; $stepNr ]
100                 Set Field [ ChecklistItems::Nr         ; $n ]
101                 Set Field [ ChecklistItems::Punkt      ; $p ]
102                 Set Field [ ChecklistItems::Erklaerung ; $e ]
103                 Commit Records [ ohne Dialog ]
104                 Go to Layout [ "96 ChecklistTemplates_Utility" ]
105                 Go to Record [ Nächster ; Verlassen nach letztem ]
106             End Loop
107         End If
108
109         Go to Layout [ "95 StepTemplates_Utility" ]
110         Go to Record [ Nächster ; Verlassen nach letztem ]
111     End Loop
112
113     Go to Layout [ "94 SectionTemplates_Utility" ]
114     Go to Record [ Nächster ; Verlassen nach letztem ]
115 End Loop
116
117 # ---- Abschluss ----
118 Perform Script [ "S-09 Verlauf schreiben" ; Parameter:
       JSONSetElement ( "{}" ;
         [ "projekt" ; $projektID ; JSONString ] ;
         [ "bereich" ; "Projekt"  ; JSONString ] ;
         [ "text"    ; "Projekt angelegt, 24-Monats-Bogen erzeugt" ; JSONString ] ) ]
119 Perform Script [ "S-08 Kennzahlen aktualisieren" ; Parameter: $projektID ]
120 Go to Layout [ "90 Projects_Utility" ]
121 Enter Find Mode [ ] · Set Field [ Projects::__pkProjectID ; $projektID ] · Perform Find
122 Go to Layout [ "03 Projektakte" ]
```

> **Warum JSON statt vieler Variablen?** Zwischen `Go to Layout` verliert man den
> Zugriff auf die Felder der Vorlagentabelle. Alle Werte in einer JSON-Variablen
> zu sammeln, ist kürzer und weniger fehleranfällig als elf einzelne
> `Set Variable`-Schritte — und man sieht auf einen Blick, was übertragen wird.

> **Laufzeit:** etwa 80 Datensätze. Auf dem Server über *Perform Script on Server*
> unter einer Sekunde, lokal spürbar länger. In S-03 Zeile 1 deshalb später
> ergänzen: bei `Get ( SystemPlatform )` ≠ Server per PSOS ausführen.

---

## S-04 · Abschnitt starten / abschließen

Setzt den Status, die Ist-Termine und die denormalisierten `s_`-Felder am Projekt.
**Verhindert nichts** — es fragt nur nach, wenn Punkte offen sind.

```
 1  Set Error Capture [ On ]
 2  Set Variable [ $modus ; Get ( ScriptParameter ) ]      # "start" oder "ende"
 3  Set Variable [ $sectionID ; Sections::__pkSectionID ]
 4  Set Variable [ $projektID ; Sections::_fkProjectID ]
 5
 6  If [ $modus = "start" ]
 7      Set Field [ Sections::Status    ; "läuft" ]
 8      Set Field [ Sections::Ist_Start ; Get ( CurrentDate ) ]
 9      Set Variable [ $text ; "Abschnitt „" & Sections::Name & "“ begonnen" ]
10  Else
11      If [ Sections::c_PunkteGesamt - Sections::c_PunkteErledigt > 0 ]
12          Show Custom Dialog [ "Noch offene Punkte" ;
              Sections::c_PunkteGesamt - Sections::c_PunkteErledigt &
              " Punkte der Checkliste sind offen. Trotzdem abschließen?" ;
              Schaltflächen: "Abschließen" , "Zurück" ]
13          If [ Get ( LastMessageChoice ) = 2 ] Exit Script  End If
14      End If
15      Set Field [ Sections::Status   ; "abgeschlossen" ]
16      Set Field [ Sections::Ist_Ende ; Get ( CurrentDate ) ]
17      Set Variable [ $text ; "Abschnitt „" & Sections::Name & "“ abgeschlossen" ]
18  End If
19  Commit Records [ ohne Dialog ]
20
21  Perform Script [ "S-08 Kennzahlen aktualisieren" ; Parameter: $projektID ]
22  Perform Script [ "S-09 Verlauf schreiben" ; Parameter:
       JSONSetElement ( "{}" ;
         [ "projekt" ; $projektID ; JSONString ] ;
         [ "bereich" ; "Abschnitt" ; JSONString ] ;
         [ "text"    ; $text ; JSONString ] ) ]
```

> **Zeile 12 ist der Leitgedanke in einer Zeile.** Die Schaltfläche „Abschließen"
> steht links und ist die Vorgabe. Es gibt keinen Weg, an dem das Skript den
> Abschluss verweigert.

---

## S-05 · Hinweise erzeugen

Erzeugt die Hinweise beider Stufen. Läuft nachts über alle Projekte und
zusätzlich nach jedem Statuswechsel für das einzelne Projekt.

**Aufbau:** Für jede Regel wird geprüft, ob sie zutrifft. Trifft sie zu und
existiert noch kein Hinweis mit diesem `Schluessel` (auch kein weggeklickter),
werden **zwei** Datensätze angelegt — einer je Stufe. Trifft sie nicht mehr zu,
wird der Hinweis auf `Zurueckgezogen = 1` gesetzt.

```
 1  Set Error Capture [ On ]
 2  Set Variable [ $projektID ; Get ( ScriptParameter ) ]
 3  Go to Layout [ "90 Projects_Utility" ]
 4  Enter Find Mode [ ] · Set Field [ Projects::__pkProjectID ; $projektID ] · Perform Find
 5
 6  # ---- Regel 1: Ethikvotum fehlt ----
 7  Set Variable [ $trifftZu ;
       ( Projects::Ethikpflicht ≠ "nein" ) and IsEmpty ( Projects::EthikVotum )
       and Projects::s_AktuellerAbschnittNr ≥ 3 ]
 8  Perform Script [ "S-07 Hinweis setzen" ; Parameter:
       JSONSetElement ( "{}" ;
         [ "projekt"  ; $projektID   ; JSONString ] ;
         [ "schluessel" ; "ETHIK_FEHLT" ; JSONString ] ;
         [ "trifftzu" ; $trifftZu    ; JSONNumber ] ;
         [ "rang"     ; 1            ; JSONNumber ] ;
         [ "titel"    ; "Kein Ethikvotum hinterlegt" ; JSONString ] ;
         [ "textF"    ; "Für dieses Projekt ist kein Votum eingetragen, die Erhebung läuft aber schon. Falls das Vorhaben nicht zustimmungspflichtig ist, klicken Sie den Hinweis einfach weg." ; JSONString ] ;
         [ "textL"    ; "Erhebung läuft ohne hinterlegtes Ethikvotum. Kurz nachfragen, ob das geklärt ist — das lässt sich später schlecht nachholen." ; JSONString ] ) ]
 9
10  # ---- Regel 2: lange still ----
11  Set Variable [ $tage ; Get ( CurrentDate ) - Projects::s_LetzteAktivitaet ]
12  Set Variable [ $trifftZu ; $tage > Projects_zz_Utility::StilleFristTage ]
13  Perform Script [ "S-07 Hinweis setzen" ; Parameter: … Schlüssel "STILL" … ]
14
15  # ---- Regel 3: Abschnitt läuft länger als der Schwerpunkt ----
16  Set Variable [ $trifftZu ; Projects::c_Verzug > 1 ]
17  Perform Script [ "S-07 Hinweis setzen" ; Parameter: … Schlüssel "VERZUG" … ]
18
19  # ---- Regel 4: kein Abschnitt begonnen, aber Monat > 1 ----
20  # ---- Regel 5: Analyse ohne festgehaltenen Datenstand ----
21  # ---- Regel 6: Abschnitt Veröffentlichen erreicht, kein Manuskript ----
22  # ---- Regel 7: Ergebnis seit über 30 Tagen im Review ----
```

Die vollständigen Texte aller sieben Regeln in beiden Fassungen liegen in
`seed/Nudgetexte.tab` und lassen sich von dort übernehmen.

> **Warum zwei Datensätze je Regel?** Weil derselbe Sachverhalt für Forschende und
> für die Betreuung unterschiedlich formuliert gehört — und weil jede Stufe ihren
> Hinweis unabhängig wegklicken können muss. Die Betreuung soll nicht mitbekommen,
> was die forschende Person weggeklickt hat, und umgekehrt.

## S-06 · Hinweis wegklicken

```
 1  Set Field [ Nudges::Weggeklickt    ; 1 ]
 2  Set Field [ Nudges::Weggeklickt_am ; Get ( CurrentDate ) ]
 3  Set Field [ Nudges::Weggeklickt_von ; Get ( AccountName ) ]
 4  Commit Records [ ohne Dialog ]
 5  Refresh Portal [ Objektname: "portal_hinweise" ]
```

Kein Verlaufseintrag. Einen Hinweis wegzuklicken ist kein Vorgang, über den
jemand Rechenschaft ablegen muss — das würde die Zusage „passt so bei uns"
unterlaufen.

## S-07 · Hinweis setzen *(Hilfsskript)*

```
 1  Set Variable [ $p ; Get ( ScriptParameter ) ]
 2  Set Variable [ $vorhanden ; ExecuteSQL (
       "SELECT COUNT(*) FROM \"Nudges\" WHERE \"_fkProjectID\" = ? AND \"Schluessel\" = ?" ;
       "" ; "" ; JSONGetElement ( $p ; "projekt" ) ; JSONGetElement ( $p ; "schluessel" ) ) ]
 3  If [ JSONGetElement ( $p ; "trifftzu" ) = 1 and $vorhanden = 0 ]
 4      Go to Layout [ "97 Nudges_Utility" ]
 5      # Datensatz für die Stufe Forschende
 6      New Record/Request
 7      Set Field [ Nudges::_fkProjectID ; JSONGetElement ( $p ; "projekt" ) ]
 8      Set Field [ Nudges::Schluessel   ; JSONGetElement ( $p ; "schluessel" ) ]
 9      Set Field [ Nudges::Stufe ; "Forschende" ]
10      Set Field [ Nudges::Titel ; JSONGetElement ( $p ; "titel" ) ]
11      Set Field [ Nudges::Text  ; JSONGetElement ( $p ; "textF" ) ]
12      Set Field [ Nudges::Rang  ; JSONGetElement ( $p ; "rang" ) ]
13      Commit Records [ ohne Dialog ]
14      # Datensatz für die Stufe Leitung
15      New Record/Request
16      … identisch, aber Stufe "Leitung" und Text aus "textL" …
17      Commit Records [ ohne Dialog ]
18  Else If [ JSONGetElement ( $p ; "trifftzu" ) = 0 and $vorhanden > 0 ]
19      # Regel trifft nicht mehr zu — Hinweise zurückziehen
20      Go to Layout [ "97 Nudges_Utility" ]
21      Enter Find Mode [ ]
22      Set Field [ Nudges::_fkProjectID ; JSONGetElement ( $p ; "projekt" ) ]
23      Set Field [ Nudges::Schluessel   ; JSONGetElement ( $p ; "schluessel" ) ]
24      Perform Find
25      Replace Field Contents [ Nudges::Zurueckgezogen ; 1 ; ohne Dialog ]
26  End If
```

---

## S-08 · Kennzahlen aktualisieren

Schreibt die `c_`-Werte in die `s_`-Felder. Ohne Parameter über alle Projekte
(Nachtlauf), mit Projekt-ID nur für eines.

```
 1  Set Error Capture [ On ]
 2  Freeze Window
 3  Set Variable [ $einzel ; Get ( ScriptParameter ) ]
 4  Go to Layout [ "90 Projects_Utility" ]
 5  If [ IsEmpty ( $einzel ) ]
 6      Show All Records
 7  Else
 8      Enter Find Mode [ ] · Set Field [ Projects::__pkProjectID ; $einzel ] · Perform Find
 9  End If
10  Go to Record [ Erster ]
11  Loop
12      # laufenden Abschnitt ermitteln
13      Set Variable [ $lauf ; ExecuteSQL (
          "SELECT \"Nr\",\"Name\",\"Schwerpunkt_Bis_Monat\",\"__pkSectionID\"
             FROM \"Sections\"
            WHERE \"_fkProjectID\" = ? AND \"Status\" = 'läuft'" ;
          "|" ; "" ; Projects::__pkProjectID ) ]
14      Set Field [ Projects::s_AktuellerAbschnittNr   ; GetValue ( Substitute ( $lauf ; "|" ; ¶ ) ; 1 ) ]
15      Set Field [ Projects::s_AktuellerAbschnittName ; GetValue ( Substitute ( $lauf ; "|" ; ¶ ) ; 2 ) ]
16      Set Field [ Projects::s_AktuellerAbschnittSchwerpunktBis ; GetValue ( Substitute ( $lauf ; "|" ; ¶ ) ; 3 ) ]
17      Set Field [ Projects::s_AktuelleSectionID     ; GetValue ( Substitute ( $lauf ; "|" ; ¶ ) ; 4 ) ]
18      # letzte Aktivität aus dem Verlauf
19      Set Field [ Projects::s_LetzteAktivitaet ; GetAsDate ( ExecuteSQL (
          "SELECT MAX(\"Zeitstempel\") FROM \"ActivityLog\" WHERE \"_fkProjectID\" = ?" ;
          "" ; "" ; Projects::__pkProjectID ) ) ]
20      # abgeleitete Werte übernehmen
21      Set Field [ Projects::s_MonatImProjekt ; Projects::c_MonatImProjekt ]
22      Set Field [ Projects::s_Verzug         ; Projects::c_Verzug ]
23      Set Field [ Projects::s_EinreichungVoraussichtlich ; Projects::c_EinreichungVoraussichtlich ]
24      Set Field [ Projects::s_Zustand        ; Projects::c_Zustand ]
25      Set Field [ Projects::s_PunkteOffen    ; GetAsNumber ( ExecuteSQL (
          "SELECT SUM(\"IstOffen\") FROM \"ChecklistItems\" WHERE \"_fkProjectID\" = ?" ;
          "" ; "" ; Projects::__pkProjectID ) ) ]
26      Set Field [ Projects::s_PunkteGesamt   ; GetAsNumber ( ExecuteSQL (
          "SELECT SUM(\"IstGezaehlt\") FROM \"ChecklistItems\" WHERE \"_fkProjectID\" = ?" ;
          "" ; "" ; Projects::__pkProjectID ) ) ]
27      Commit Records [ ohne Dialog ]
28      Perform Script [ "S-05 Hinweise erzeugen" ; Parameter: Projects::__pkProjectID ]
29      Go to Record [ Nächster ; Verlassen nach letztem ]
30  End Loop
```

> **Reihenfolge beachten:** Zeilen 14–19 müssen vor Zeile 21 stehen, weil `c_Verzug`
> und `c_Zustand` auf `s_AktuellerAbschnittSchwerpunktBis` und
> `s_LetzteAktivitaet` zugreifen. Wer die Reihenfolge dreht, bekommt jede Nacht
> die Werte von gestern.

## S-09 · Verlauf schreiben *(Hilfsskript)*

```
 1  Set Variable [ $p ; Get ( ScriptParameter ) ]
 2  Set Variable [ $layoutVorher ; Get ( LayoutName ) ]
 3  Go to Layout [ "98 ActivityLog_Utility" ]
 4  New Record/Request
 5  Set Field [ ActivityLog::_fkProjectID ; JSONGetElement ( $p ; "projekt" ) ]
 6  Set Field [ ActivityLog::Bereich      ; JSONGetElement ( $p ; "bereich" ) ]
 7  Set Field [ ActivityLog::Text         ; JSONGetElement ( $p ; "text" ) ]
 8  Set Field [ ActivityLog::Bezug_ID     ; JSONGetElement ( $p ; "bezug" ) ]
 9  Commit Records [ ohne Dialog ]
10  Go to Layout [ $layoutVorher ]
```

Zeitstempel und Konto füllt FileMaker über die Automatikoptionen der Felder —
zwei Zeilen weniger.

---

## Übersicht der Aufrufe

```
BeimÖffnen ──▶ S-01 Start ──▶ S-02 Meine Projekte laden

Knopf „Neues Projekt" ──▶ S-03 Projekt anlegen ──┬─▶ S-08 ──▶ S-05 ──▶ S-07
                                                  └─▶ S-09

Knopf „Abschnitt starten/abschließen" ──▶ S-04 ──┬─▶ S-08 ──▶ S-05 ──▶ S-07
                                                  └─▶ S-09

Knopf „passt" ──▶ S-06

Serverzeitplan nachts ──▶ S-08 (alle Projekte) ──▶ S-05 ──▶ S-07
Serverzeitplan montags ──▶ S-10 Wochenimpuls (Doc 07 §4)
```

Neun Skripte, davon drei reine Hilfsskripte. Jede Fachlogik existiert genau
einmal.
