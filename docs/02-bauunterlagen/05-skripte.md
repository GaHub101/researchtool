# Skripte

**Elf Skripte.** Der einzige Posten, der sich nicht importieren lässt —
FileMaker-Skripte müssen von Hand zusammengeklickt werden. Deshalb sind sie so
wenige und so kurz wie möglich gehalten: S-09 und S-11 werden von den anderen
aufgerufen, statt dass jedes Skript seine eigene Fassung mitbringt.

## Vor dem Anfangen

Die Schrittnamen stehen deutsch. Falls Ihre Installation englisch läuft:

| Deutsch | Englisch | Deutsch | Englisch |
|---|---|---|---|
| Variable setzen | Set Variable | Feldwert setzen | Set Field |
| Gehe zu Layout | Go to Layout | Neuer Datensatz/Abfrage | New Record/Request |
| Schleife / Ende Schleife | Loop / End Loop | Gehe zu Datensatz | Go to Record |
| Suchenmodus aktivieren | Enter Find Mode | Suchen | Perform Find |
| Eigenes Dialogfeld anzeigen | Show Custom Dialog | Fenster fixieren | Freeze Window |
| Schreibe Änderung Datens. | Commit Records | Fehleraufzeichnung setzen | Set Error Capture |
| Alle Datensätze anzeigen | Show All Records | Datensätze sortieren | Sort Records |
| Skript ausführen | Perform Script | Feldinhalt ersetzen | Replace Field Contents |

**Alle Skripte** beginnen mit `Fehleraufzeichnung setzen [ Ein ]` und
`Fenster fixieren`, damit ein Abbruch nicht als FileMaker-Fehlermeldung beim
Anwender landet.

**Hilfslayouts:** Skripte, die Datensätze in einer anderen Tabelle anlegen,
brauchen dort einen Kontext. Dafür gibt es die Layouts `90`–`99` (Doc 06 §8) —
leere Listenlayouts ohne Objekte, nur zum Wechseln.

**Mehrbenutzerbetrieb:** Nach **jedem** Anlegen oder Ändern folgt
`Schreibe Änderung Datens. [ ohne Dialog ]`. Ein nicht geschriebener Datensatz
bleibt für andere gesperrt — bei 8 bis 12 gleichzeitigen Anwendern ist das die
häufigste Ursache für „Datensatz wird von … benutzt".

---

## S-01 · Start

*Auslöser: Datei ▸ Dateioptionen ▸ Beim Öffnen*

Setzt die globalen Felder, von denen Portalfilter, Sichtbarkeiten **und die
Zugriffsrechte** abhängen, und schickt in die richtige Einstiegsseite.

```
 1  Fehleraufzeichnung setzen [ Ein ]
 2  Fenster fixieren
 3  Variable setzen [ $konto ; Hole ( Kontoname ) ]
 4  Variable setzen [ $set   ; Hole ( KontoBerechtigungsname ) ]
 5  Gehe zu Layout [ "90 Projekte_Hilfslayout" ]
 6  Feldwert setzen [ Projekte::g_MeinKonto ; $konto ]
 7  Feldwert setzen [ Projekte::g_MeineStufe ;
       Wenn ( $set = "Forschungsleitung" ; "Leitung" ; "Forschende" ) ]
 8  # Person zum Konto suchen
 9  Variable setzen [ $personID ; AuswerteSQL (
       "SELECT \"__pkPersonID\" FROM \"Personen\" WHERE \"Kontoname\" = ?" ;
       "" ; "" ; $konto ) ]
10  Feldwert setzen [ Projekte::g_MeinePersonID ; $personID ]
11  Wenn [ IstLeer ( $personID ) und $set ≠ "Administration" ]
12      Eigenes Dialogfeld anzeigen [ "Konto nicht zugeordnet" ;
          "Zu Ihrem Konto ist keine Person hinterlegt. Bitte bei der Administration melden." ]
13      Skript verlassen
14  Ende (wenn)
15  Skript ausführen [ "S-02 Meine Projekte laden" ]
```

> **Zeile 10 ist sicherheitsrelevant.** `g_MeinePersonID` steuert nicht nur
> Portalfilter, sondern auch die Zugriffsformeln F-24. Bleibt das Feld leer, sieht
> ein Konto der Stufe *Forschende* **kein** Projekt — das ist die richtige
> Vorgabe: im Zweifel nichts zeigen. Zeile 11 fängt den Fall mit einer
> verständlichen Meldung ab, statt die Person ratlos vor einer leeren Liste
> stehen zu lassen.

> Zeile 7 ist die einzige Stelle, an der die Stufe technisch entschieden wird.
> Wer die Rechtesets umbenennt, muss hier nachziehen.

---

## S-02 · Meine Projekte laden

```
 1  Fehleraufzeichnung setzen [ Ein ]
 2  Fenster fixieren
 3  Gehe zu Layout [ "90 Projekte_Hilfslayout" ]
 4  Wenn [ Projekte::g_MeineStufe = "Leitung" ]
 5      Alle Datensätze anzeigen
 6  Sonst
 7      Variable setzen [ $ids ; AuswerteSQL (
          "SELECT DISTINCT \"_fkProjektID\" FROM \"Projektbeteiligte\" WHERE \"_fkPersonID\" = ?" ;
          "" ; "" ; Projekte::g_MeinePersonID ) ]
 8      Wenn [ IstLeer ( $ids ) ]
 9          Eigenes Dialogfeld anzeigen [ "Noch kein Projekt" ;
              "Ihnen ist noch kein Projekt zugeordnet. Wenden Sie sich an die Forschungsleitung." ]
10          Skript verlassen
11      Ende (wenn)
12      Suchenmodus aktivieren [ Pause: Aus ]
13      Feldwert setzen [ Projekte::__pkProjektID ; $ids ]
14      Suchen
15  Ende (wenn)
16  Datensätze sortieren [ nach Projekte::Titel, aufsteigend ; ohne Dialog ]
17  Wenn [ Projekte::g_MeineStufe = "Leitung" ]
18      Gehe zu Layout [ "02 Meine Projekte" ]
19  Sonst wenn [ Hole ( AnzahlGefundene ) = 1 ]
20      Gehe zu Layout [ "01 Mein Projekt" ]
21  Sonst
22      Gehe zu Layout [ "06 Projektauswahl" ]
23  Ende (wenn)
```

> **Zeile 13 ist ein Trick:** Im Suchmodus erzeugt jede Zeile eines mehrzeiligen
> Werts eine eigene Suchabfrage — das ergibt eine ODER-Verknüpfung ohne Schleife.
>
> **Zeile 5 sieht nach einem Loch aus, ist keins.** `Alle Datensätze anzeigen`
> liefert nur, was das Rechteset erlaubt. Für die Stufe *Forschende* stünden dort
> `<Kein Zugriff>`-Platzhalter — deshalb der getrennte Zweig. Die Suche ist
> Bequemlichkeit; die Sicherheit liegt im Rechteset (Doc 07).

---

## S-03 · Projekt anlegen

**Das Herzstück.** Erzeugt aus Titel und Startdatum den kompletten
24-Monats-Bogen: 5 Abschnitte, 13 Schritte, 59 Punkte plus 13 Überschriftszeilen —
mit allen Texten der Wissensschicht.

Der Knopf ist nur für die Stufe *Leitung* und die Administration sichtbar.

```
 1  Fehleraufzeichnung setzen [ Ein ]
 2  Fenster fixieren
 3  Eigenes Dialogfeld anzeigen [ "Neues Projekt" ; "Titel:" ; Projekte::g_Eingabe1 ]
 4  Wenn [ Hole ( LetzteMeldungswahl ) = 2 ]  Skript verlassen  Ende (wenn)
 5
 6  # ---- Projekt anlegen ----
 7  Gehe zu Layout [ "90 Projekte_Hilfslayout" ]
 8  Neuer Datensatz/Abfrage
 9  Feldwert setzen [ Projekte::Titel ; Projekte::g_Eingabe1 ]
10  Feldwert setzen [ Projekte::Startdatum ; Hole ( AktuellesDatum ) ]
11  Schreibe Änderung Datens. [ ohne Dialog ]
12  Variable setzen [ $projektID ; Projekte::__pkProjektID ]
13  Variable setzen [ $start     ; Projekte::Startdatum ]
14
15  # ---- Anlegende Person als beteiligt eintragen ----
16  Gehe zu Layout [ "99 Beteiligte_Hilfslayout" ]
17  Neuer Datensatz/Abfrage
18  Feldwert setzen [ Projektbeteiligte::_fkProjektID ; $projektID ]
19  Feldwert setzen [ Projektbeteiligte::_fkPersonID  ; Projekte::g_MeinePersonID ]
20  Feldwert setzen [ Projektbeteiligte::RACI ; "Rechenschaftspflichtig" ]
21  Schreibe Änderung Datens. [ ohne Dialog ]
22
23  # ---- Schleife über die Abschnittsvorlagen ----
24  Gehe zu Layout [ "94 VorlageAbschnitte_Hilfslayout" ]
25  Alle Datensätze anzeigen
26  Datensätze sortieren [ nach VorlageAbschnitte::Nr ; ohne Dialog ]
27  Gehe zu Datensatz [ Erster ]
28  Schleife
29      Variable setzen [ $a ; JSONSetElement ( "{}" ;
            [ "nr"     ; VorlageAbschnitte::Nr                    ; JSONNumber ] ;
            [ "name"   ; VorlageAbschnitte::Name                  ; JSONString ] ;
            [ "leitfr" ; VorlageAbschnitte::Leitfrage             ; JSONString ] ;
            [ "bv"     ; VorlageAbschnitte::Band_Von_Monat        ; JSONNumber ] ;
            [ "bb"     ; VorlageAbschnitte::Band_Bis_Monat        ; JSONNumber ] ;
            [ "sv"     ; VorlageAbschnitte::Schwerpunkt_Von_Monat ; JSONNumber ] ;
            [ "sb"     ; VorlageAbschnitte::Schwerpunkt_Bis_Monat ; JSONNumber ] ;
            [ "zweck"  ; VorlageAbschnitte::Zweck                 ; JSONString ] ;
            [ "stolp"  ; VorlageAbschnitte::Stolpersteine         ; JSONString ] ;
            [ "hf"     ; VorlageAbschnitte::Hinweis_Forschende    ; JSONString ] ;
            [ "hl"     ; VorlageAbschnitte::Hinweis_Leitung       ; JSONString ] ) ]
30      Variable setzen [ $abschnittNr ; VorlageAbschnitte::Nr ]
31
32      Gehe zu Layout [ "91 Abschnitte_Hilfslayout" ]
33      Neuer Datensatz/Abfrage
34      Feldwert setzen [ Abschnitte::_fkProjektID ; $projektID ]
35      Feldwert setzen [ Abschnitte::Nr        ; JSONGetElement ( $a ; "nr" ) ]
36      Feldwert setzen [ Abschnitte::Name      ; JSONGetElement ( $a ; "name" ) ]
37      Feldwert setzen [ Abschnitte::Leitfrage ; JSONGetElement ( $a ; "leitfr" ) ]
38      Feldwert setzen [ Abschnitte::Band_Von_Monat        ; JSONGetElement ( $a ; "bv" ) ]
39      Feldwert setzen [ Abschnitte::Band_Bis_Monat        ; JSONGetElement ( $a ; "bb" ) ]
40      Feldwert setzen [ Abschnitte::Schwerpunkt_Von_Monat ; JSONGetElement ( $a ; "sv" ) ]
41      Feldwert setzen [ Abschnitte::Schwerpunkt_Bis_Monat ; JSONGetElement ( $a ; "sb" ) ]
42      Feldwert setzen [ Abschnitte::Zweck              ; JSONGetElement ( $a ; "zweck" ) ]
43      Feldwert setzen [ Abschnitte::Stolpersteine      ; JSONGetElement ( $a ; "stolp" ) ]
44      Feldwert setzen [ Abschnitte::Hinweis_Forschende ; JSONGetElement ( $a ; "hf" ) ]
45      Feldwert setzen [ Abschnitte::Hinweis_Leitung    ; JSONGetElement ( $a ; "hl" ) ]
46      Feldwert setzen [ Abschnitte::Soll_Start ;
            Datum ( Monat ( $start ) + JSONGetElement ( $a ; "bv" ) - 1 ; Tag ( $start ) ; Jahr ( $start ) ) ]
47      Feldwert setzen [ Abschnitte::Soll_Ende ;
            Datum ( Monat ( $start ) + JSONGetElement ( $a ; "bb" ) ; Tag ( $start ) - 1 ; Jahr ( $start ) ) ]
48      Feldwert setzen [ Abschnitte::Status ; "offen" ]
49      Schreibe Änderung Datens. [ ohne Dialog ]
50      Variable setzen [ $abschnittID ; Abschnitte::__pkAbschnittID ]
51
52      # ---- Schleife über die Schrittvorlagen dieses Abschnitts ----
53      Gehe zu Layout [ "95 VorlageSchritte_Hilfslayout" ]
54      Suchenmodus aktivieren · Feldwert setzen [ VorlageSchritte::AbschnittNr ; $abschnittNr ] · Suchen
55      Datensätze sortieren [ nach VorlageSchritte::Nr ; ohne Dialog ]
56      Gehe zu Datensatz [ Erster ]
57      Schleife
58          Variable setzen [ $s ; JSONSetElement ( "{}" ;
                [ "nr"    ; VorlageSchritte::Nr                 ; JSONNumber ] ;
                [ "name"  ; VorlageSchritte::Name               ; JSONString ] ;
                [ "zweck" ; VorlageSchritte::Zweck              ; JSONString ] ;
                [ "stolp" ; VorlageSchritte::Stolpersteine      ; JSONString ] ;
                [ "hf"    ; VorlageSchritte::Hinweis_Forschende ; JSONString ] ;
                [ "hl"    ; VorlageSchritte::Hinweis_Leitung    ; JSONString ] ) ]
59          Variable setzen [ $schrittNr ; VorlageSchritte::Nr ]
60
61          Gehe zu Layout [ "92 Schritte_Hilfslayout" ]
62          Neuer Datensatz/Abfrage
63          Feldwert setzen [ Schritte::_fkAbschnittID ; $abschnittID ]
64          Feldwert setzen [ Schritte::_fkProjektID   ; $projektID ]
65          Feldwert setzen [ Schritte::Nr    ; JSONGetElement ( $s ; "nr" ) ]
66          Feldwert setzen [ Schritte::Name  ; JSONGetElement ( $s ; "name" ) ]
67          Feldwert setzen [ Schritte::Zweck ; JSONGetElement ( $s ; "zweck" ) ]
68          Feldwert setzen [ Schritte::Stolpersteine      ; JSONGetElement ( $s ; "stolp" ) ]
69          Feldwert setzen [ Schritte::Hinweis_Forschende ; JSONGetElement ( $s ; "hf" ) ]
70          Feldwert setzen [ Schritte::Hinweis_Leitung    ; JSONGetElement ( $s ; "hl" ) ]
71          Feldwert setzen [ Schritte::Status ; "offen" ]
72          Schreibe Änderung Datens. [ ohne Dialog ]
73          Variable setzen [ $schrittID   ; Schritte::__pkSchrittID ]
74          Variable setzen [ $schrittName ; Schritte::Name ]
75
76          # ---- Überschriftszeile (Ersatz für verschachtelte Portale) ----
77          Gehe zu Layout [ "93 Punkte_Hilfslayout" ]
78          Neuer Datensatz/Abfrage
79          Feldwert setzen [ Punkte::_fkSchrittID   ; $schrittID ]
80          Feldwert setzen [ Punkte::_fkAbschnittID ; $abschnittID ]
81          Feldwert setzen [ Punkte::_fkProjektID   ; $projektID ]
82          Feldwert setzen [ Punkte::SchrittNr ; $schrittNr ]
83          Feldwert setzen [ Punkte::Nr        ; 0 ]
84          Feldwert setzen [ Punkte::Punkt     ; $schrittName ]
85          Feldwert setzen [ Punkte::IstUeberschrift ; 1 ]
86          Schreibe Änderung Datens. [ ohne Dialog ]
87
88          # ---- Schleife über die Punktvorlagen ----
89          Gehe zu Layout [ "96 VorlagePunkte_Hilfslayout" ]
90          Suchenmodus aktivieren
91          Feldwert setzen [ VorlagePunkte::AbschnittNr ; $abschnittNr ]
92          Feldwert setzen [ VorlagePunkte::SchrittNr   ; $schrittNr ]
93          Suchen
94          Datensätze sortieren [ nach VorlagePunkte::Nr ; ohne Dialog ]
95          Wenn [ Hole ( AnzahlGefundene ) > 0 ]
96              Gehe zu Datensatz [ Erster ]
97              Schleife
98                  Variable setzen [ $p ; VorlagePunkte::Punkt ]
99                  Variable setzen [ $e ; VorlagePunkte::Erklaerung ]
100                 Variable setzen [ $n ; VorlagePunkte::Nr ]
101                 Gehe zu Layout [ "93 Punkte_Hilfslayout" ]
102                 Neuer Datensatz/Abfrage
103                 Feldwert setzen [ Punkte::_fkSchrittID   ; $schrittID ]
104                 Feldwert setzen [ Punkte::_fkAbschnittID ; $abschnittID ]
105                 Feldwert setzen [ Punkte::_fkProjektID   ; $projektID ]
106                 Feldwert setzen [ Punkte::SchrittNr  ; $schrittNr ]
107                 Feldwert setzen [ Punkte::Nr         ; $n ]
108                 Feldwert setzen [ Punkte::Punkt      ; $p ]
109                 Feldwert setzen [ Punkte::Erklaerung ; $e ]
110                 Schreibe Änderung Datens. [ ohne Dialog ]
111                 Gehe zu Layout [ "96 VorlagePunkte_Hilfslayout" ]
112                 Gehe zu Datensatz [ Nächster ; Verlassen nach letztem ]
113             Ende Schleife
114         Ende (wenn)
115         Gehe zu Layout [ "95 VorlageSchritte_Hilfslayout" ]
116         Gehe zu Datensatz [ Nächster ; Verlassen nach letztem ]
117     Ende Schleife
118     Gehe zu Layout [ "94 VorlageAbschnitte_Hilfslayout" ]
119     Gehe zu Datensatz [ Nächster ; Verlassen nach letztem ]
120 Ende Schleife
121
122 # ---- Abschluss ----
123 Skript ausführen [ "S-11 Zugriffsliste aktualisieren" ; Parameter: $projektID ]
124 Skript ausführen [ "S-09 Verlauf schreiben" ; Parameter:
       JSONSetElement ( "{}" ;
         [ "projekt" ; $projektID ; JSONString ] ;
         [ "bereich" ; "Projekt"  ; JSONString ] ;
         [ "text"    ; "Projekt angelegt, 24-Monats-Bogen erzeugt" ; JSONString ] ) ]
125 Skript ausführen [ "S-08 Kennzahlen aktualisieren" ; Parameter: $projektID ]
126 Gehe zu Layout [ "90 Projekte_Hilfslayout" ]
127 Suchenmodus aktivieren · Feldwert setzen [ Projekte::__pkProjektID ; $projektID ] · Suchen
128 Gehe zu Layout [ "03 Projektakte" ]
```

> **Zeile 123 darf nicht fehlen.** Ohne den Aufruf von S-11 bleiben alle
> `s_ZugriffIDs` leer — dann sieht niemand der Stufe *Forschende* dieses Projekt,
> auch die zugeordneten Personen nicht.

> **Warum JSON statt vieler Variablen?** Zwischen `Gehe zu Layout` verliert man
> den Zugriff auf die Felder der Vorlagentabelle. Alle Werte in einer
> JSON-Variablen zu sammeln ist kürzer und weniger fehleranfällig als elf einzelne
> Variablen — und man sieht auf einen Blick, was übertragen wird.

> **Laufzeit:** rund 90 Datensätze. Später ergänzen: das Skript per
> *Skript auf Server ausführen* starten. Dann dauert es unter einer Sekunde und
> belastet weder Arbeitsplatz noch Netz.

---

## S-04 · Abschnitt starten / abschließen

Setzt Status, Ist-Termine und die abgeleiteten Werte am Projekt.
**Verhindert nichts** — es fragt nur nach, wenn Punkte offen sind.

```
 1  Fehleraufzeichnung setzen [ Ein ]
 2  Variable setzen [ $modus ; Hole ( Skriptparameter ) ]      # "start" oder "ende"
 3  Variable setzen [ $abschnittID ; Abschnitte::__pkAbschnittID ]
 4  Variable setzen [ $projektID   ; Abschnitte::_fkProjektID ]
 5
 6  Wenn [ $modus = "start" ]
 7      Feldwert setzen [ Abschnitte::Status    ; "läuft" ]
 8      Feldwert setzen [ Abschnitte::Ist_Start ; Hole ( AktuellesDatum ) ]
 9      Variable setzen [ $text ; "Abschnitt „" & Abschnitte::Name & "“ begonnen" ]
10  Sonst
11      Wenn [ Abschnitte::c_PunkteGesamt - Abschnitte::c_PunkteErledigt > 0 ]
12          Eigenes Dialogfeld anzeigen [ "Noch offene Punkte" ;
              Abschnitte::c_PunkteGesamt - Abschnitte::c_PunkteErledigt &
              " Punkte der Checkliste sind offen. Trotzdem abschließen?" ;
              Schaltflächen: "Abschließen" , "Zurück" ]
13          Wenn [ Hole ( LetzteMeldungswahl ) = 2 ]  Skript verlassen  Ende (wenn)
14      Ende (wenn)
15      Feldwert setzen [ Abschnitte::Status   ; "abgeschlossen" ]
16      Feldwert setzen [ Abschnitte::Ist_Ende ; Hole ( AktuellesDatum ) ]
17      Variable setzen [ $text ; "Abschnitt „" & Abschnitte::Name & "“ abgeschlossen" ]
18  Ende (wenn)
19  Schreibe Änderung Datens. [ ohne Dialog ]
20
21  Skript ausführen [ "S-08 Kennzahlen aktualisieren" ; Parameter: $projektID ]
22  Skript ausführen [ "S-09 Verlauf schreiben" ; Parameter:
       JSONSetElement ( "{}" ;
         [ "projekt" ; $projektID  ; JSONString ] ;
         [ "bereich" ; "Abschnitt" ; JSONString ] ;
         [ "text"    ; $text       ; JSONString ] ) ]
```

> **Zeile 12 ist der Leitgedanke in einer Zeile.** Die Schaltfläche „Abschließen"
> steht links und ist die Vorgabe. Es gibt keinen Weg, auf dem das Skript den
> Abschluss verweigert.

---

## S-05 · Hinweise erzeugen

Erzeugt die Hinweise beider Stufen. Läuft nachts über alle Projekte und
zusätzlich nach jedem Statuswechsel für das einzelne Projekt.

Für jede Regel wird geprüft, ob sie zutrifft. Trifft sie zu und existiert noch
kein Hinweis mit diesem `Schluessel` (auch kein weggeklickter), werden **zwei**
Datensätze angelegt — einer je Stufe. Trifft sie nicht mehr zu, wird der Hinweis
auf `Zurueckgezogen = 1` gesetzt.

```
 1  Fehleraufzeichnung setzen [ Ein ]
 2  Variable setzen [ $projektID ; Hole ( Skriptparameter ) ]
 3  Gehe zu Layout [ "90 Projekte_Hilfslayout" ]
 4  Suchenmodus aktivieren · Feldwert setzen [ Projekte::__pkProjektID ; $projektID ] · Suchen
 5
 6  # ---- Regel 1: Ethikvotum fehlt ----
 7  Variable setzen [ $trifftZu ;
       ( Projekte::Ethikpflicht ≠ "nein" ) und IstLeer ( Projekte::EthikVotum )
       und Projekte::s_AktuellerAbschnittNr ≥ 3 ]
 8  Skript ausführen [ "S-07 Hinweis setzen" ; Parameter:
       JSONSetElement ( "{}" ;
         [ "projekt"    ; $projektID    ; JSONString ] ;
         [ "schluessel" ; "ETHIK_FEHLT" ; JSONString ] ;
         [ "trifftzu"   ; $trifftZu     ; JSONNumber ] ;
         [ "rang"       ; 1             ; JSONNumber ] ;
         [ "titel"      ; "Kein Ethikvotum hinterlegt"            ; JSONString ] ;
         [ "textF"      ; "…Text_Forschende aus Hinweistexte.tab…" ; JSONString ] ;
         [ "textL"      ; "…Text_Leitung aus Hinweistexte.tab…"    ; JSONString ] ) ]
 9
10  # ---- Regel 2: STILL ----
11  Variable setzen [ $tage ; Hole ( AktuellesDatum ) - Projekte::s_LetzteAktivitaet ]
12  Variable setzen [ $trifftZu ; $tage > Projekte_Einstellungen::StilleFristTage ]
13  Skript ausführen [ "S-07 Hinweis setzen" ; Parameter: … Schlüssel "STILL" … ]
14
15  # ---- Regel 3: VERZUG ----
16  Variable setzen [ $trifftZu ; Projekte::c_Verzug > 1 ]
17  Skript ausführen [ "S-07 Hinweis setzen" ; Parameter: … Schlüssel "VERZUG" … ]
18
19  # ---- Regeln 4 bis 7 nach demselben Muster ----
20  #   KEIN_START · KEIN_DATENSTAND · KEIN_MANUSKRIPT · PRUEFUNG_LIEGT
```

Bedingungen und die vollständigen Texte aller sieben Regeln in beiden Fassungen
stehen in `seed/Hinweistexte.tab` und lassen sich von dort übernehmen.

## S-06 · Hinweis wegklicken

```
 1  Feldwert setzen [ Hinweise::Weggeklickt     ; 1 ]
 2  Feldwert setzen [ Hinweise::Weggeklickt_am  ; Hole ( AktuellesDatum ) ]
 3  Feldwert setzen [ Hinweise::Weggeklickt_von ; Hole ( Kontoname ) ]
 4  Schreibe Änderung Datens. [ ohne Dialog ]
 5  Portal aktualisieren [ Objektname: "portal_hinweise" ]
```

Kein Verlaufseintrag. Einen Hinweis wegzuklicken ist kein Vorgang, über den
jemand Rechenschaft ablegen muss — das würde die Zusage „passt so bei uns"
unterlaufen.

## S-07 · Hinweis setzen *(Hilfsskript)*

```
 1  Variable setzen [ $p ; Hole ( Skriptparameter ) ]
 2  Variable setzen [ $vorhanden ; AuswerteSQL (
       "SELECT COUNT(*) FROM \"Hinweise\" WHERE \"_fkProjektID\" = ? AND \"Schluessel\" = ?" ;
       "" ; "" ; JSONGetElement ( $p ; "projekt" ) ; JSONGetElement ( $p ; "schluessel" ) ) ]
 3  Wenn [ JSONGetElement ( $p ; "trifftzu" ) = 1 und $vorhanden = 0 ]
 4      Gehe zu Layout [ "97 Hinweise_Hilfslayout" ]
 5      Neuer Datensatz/Abfrage
 6      Feldwert setzen [ Hinweise::_fkProjektID ; JSONGetElement ( $p ; "projekt" ) ]
 7      Feldwert setzen [ Hinweise::Schluessel   ; JSONGetElement ( $p ; "schluessel" ) ]
 8      Feldwert setzen [ Hinweise::Stufe ; "Forschende" ]
 9      Feldwert setzen [ Hinweise::Titel ; JSONGetElement ( $p ; "titel" ) ]
10      Feldwert setzen [ Hinweise::Text  ; JSONGetElement ( $p ; "textF" ) ]
11      Feldwert setzen [ Hinweise::Rang  ; JSONGetElement ( $p ; "rang" ) ]
12      Schreibe Änderung Datens. [ ohne Dialog ]
13      Neuer Datensatz/Abfrage
14      … identisch, aber Stufe "Leitung" und Text aus "textL" …
15      Schreibe Änderung Datens. [ ohne Dialog ]
16      Skript ausführen [ "S-11 Zugriffsliste aktualisieren" ; Parameter: JSONGetElement ( $p ; "projekt" ) ]
17  Sonst wenn [ JSONGetElement ( $p ; "trifftzu" ) = 0 und $vorhanden > 0 ]
18      Gehe zu Layout [ "97 Hinweise_Hilfslayout" ]
19      Suchenmodus aktivieren
20      Feldwert setzen [ Hinweise::_fkProjektID ; JSONGetElement ( $p ; "projekt" ) ]
21      Feldwert setzen [ Hinweise::Schluessel   ; JSONGetElement ( $p ; "schluessel" ) ]
22      Suchen
23      Feldinhalt ersetzen [ Hinweise::Zurueckgezogen ; 1 ; ohne Dialog ]
24  Ende (wenn)
```

---

## S-08 · Kennzahlen aktualisieren

Schreibt die `c_`-Werte in die `s_`-Felder. Ohne Parameter über alle Projekte
(Nachtlauf), mit Projekt-ID nur für eines.

```
 1  Fehleraufzeichnung setzen [ Ein ]
 2  Fenster fixieren
 3  Variable setzen [ $einzel ; Hole ( Skriptparameter ) ]
 4  Variable setzen [ $gesperrt ; "" ]
 5  Gehe zu Layout [ "90 Projekte_Hilfslayout" ]
 6  Wenn [ IstLeer ( $einzel ) ]
 7      Alle Datensätze anzeigen
 8  Sonst
 9      Suchenmodus aktivieren · Feldwert setzen [ Projekte::__pkProjektID ; $einzel ] · Suchen
10  Ende (wenn)
11  Gehe zu Datensatz [ Erster ]
12  Schleife
13      # laufenden Abschnitt ermitteln
14      Variable setzen [ $lauf ; AuswerteSQL (
          "SELECT \"Nr\",\"Name\",\"Schwerpunkt_Bis_Monat\",\"__pkAbschnittID\"
             FROM \"Abschnitte\"
            WHERE \"_fkProjektID\" = ? AND \"Status\" = 'läuft'" ;
          "|" ; "" ; Projekte::__pkProjektID ) ]
15      Variable setzen [ $z ; Austauschen ( $lauf ; "|" ; ¶ ) ]
16      Feldwert setzen [ Projekte::s_AktuellerAbschnittNr   ; HoleWert ( $z ; 1 ) ]
17      Feldwert setzen [ Projekte::s_AktuellerAbschnittName ; HoleWert ( $z ; 2 ) ]
18      Feldwert setzen [ Projekte::s_AktuellerAbschnittSchwerpunktBis ; HoleWert ( $z ; 3 ) ]
19      Feldwert setzen [ Projekte::s_AktuelleAbschnittID    ; HoleWert ( $z ; 4 ) ]
20      Feldwert setzen [ Projekte::s_LetzteAktivitaet ; HoleAlsDatum ( AuswerteSQL (
          "SELECT MAX(\"Zeitstempel\") FROM \"Verlauf\" WHERE \"_fkProjektID\" = ?" ;
          "" ; "" ; Projekte::__pkProjektID ) ) ]
21      Feldwert setzen [ Projekte::s_MonatImProjekt ; Projekte::c_MonatImProjekt ]
22      Feldwert setzen [ Projekte::s_Verzug         ; Projekte::c_Verzug ]
23      Feldwert setzen [ Projekte::s_EinreichungVoraussichtlich ; Projekte::c_EinreichungVoraussichtlich ]
24      Feldwert setzen [ Projekte::s_Zustand        ; Projekte::c_Zustand ]
25      Feldwert setzen [ Projekte::s_PunkteOffen    ; HoleAlsZahl ( AuswerteSQL (
          "SELECT SUM(\"IstOffen\") FROM \"Punkte\" WHERE \"_fkProjektID\" = ?" ;
          "" ; "" ; Projekte::__pkProjektID ) ) ]
26      Feldwert setzen [ Projekte::s_PunkteGesamt   ; HoleAlsZahl ( AuswerteSQL (
          "SELECT SUM(\"IstGezaehlt\") FROM \"Punkte\" WHERE \"_fkProjektID\" = ?" ;
          "" ; "" ; Projekte::__pkProjektID ) ) ]
27      Schreibe Änderung Datens. [ ohne Dialog ]
28      # ---- Mehrbenutzerbetrieb: gesperrte Datensätze merken, nicht abbrechen ----
29      Wenn [ Hole ( LetzteFehlernummer ) = 301 ]
30          Variable setzen [ $gesperrt ; $gesperrt & Projekte::Titel & ¶ ]
31          Änderung Datens. rückgängig
32      Sonst
33          Skript ausführen [ "S-05 Hinweise erzeugen" ; Parameter: Projekte::__pkProjektID ]
34      Ende (wenn)
35      Gehe zu Datensatz [ Nächster ; Verlassen nach letztem ]
36  Ende Schleife
37  Wenn [ nicht IstLeer ( $gesperrt ) ]
38      # Liste per S-09 in den Verlauf schreiben — die Administration sieht sie morgens
39  Ende (wenn)
```

> **Reihenfolge beachten:** Zeilen 16–20 müssen vor Zeile 21 stehen, weil
> `c_Verzug` und `c_Zustand` auf `s_AktuellerAbschnittSchwerpunktBis` und
> `s_LetzteAktivitaet` zugreifen. Wer die Reihenfolge dreht, bekommt jede Nacht
> die Werte von gestern.

> **Zeile 29 ist die Mehrbenutzer-Absicherung.** Fehler 301 heißt: Der Datensatz
> wird gerade von jemandem bearbeitet. Ohne diese Prüfung bräche der Nachtlauf ab
> und ließe alle folgenden Projekte unberechnet. Mit ihr wird das eine Projekt
> übersprungen und in der nächsten Nacht nachgeholt.

## S-09 · Verlauf schreiben *(Hilfsskript)*

```
 1  Variable setzen [ $p ; Hole ( Skriptparameter ) ]
 2  Variable setzen [ $layoutVorher ; Hole ( Layoutname ) ]
 3  Gehe zu Layout [ "98 Verlauf_Hilfslayout" ]
 4  Neuer Datensatz/Abfrage
 5  Feldwert setzen [ Verlauf::_fkProjektID ; JSONGetElement ( $p ; "projekt" ) ]
 6  Feldwert setzen [ Verlauf::Bereich      ; JSONGetElement ( $p ; "bereich" ) ]
 7  Feldwert setzen [ Verlauf::Text         ; JSONGetElement ( $p ; "text" ) ]
 8  Feldwert setzen [ Verlauf::Bezug_ID     ; JSONGetElement ( $p ; "bezug" ) ]
 9  Feldwert setzen [ Verlauf::s_ZugriffIDs ; AuswerteSQL (
       "SELECT \"s_BeteiligtIDs\" FROM \"Projekte\" WHERE \"__pkProjektID\" = ?" ;
       "" ; "" ; JSONGetElement ( $p ; "projekt" ) ) ]
10  Schreibe Änderung Datens. [ ohne Dialog ]
11  Gehe zu Layout [ $layoutVorher ]
```

Zeitstempel und Konto füllt FileMaker über die Automatikoptionen der Felder.
Zeile 9 setzt die Zugriffsliste sofort mit — sonst wäre der neue Eintrag für
Forschende bis zum nächsten S-11-Lauf unsichtbar.

---

## S-10 · Wochenimpuls

*Serverzeitplan, montags. Noch nicht gebaut — Etappe 10 des Umsetzungsplans.*

Schleife über `Personen` mit `Aktiv = 1`: offene Hinweise der eigenen Stufe und
fällige Aufgaben zusammenstellen, eine Mail senden. Abschaltbar über
`zz_Einstellungen::WochenimpulsAktiv`.

Zwei Fassungen, je nach `Personen::Stufe` — für Forschende der eigene Stand plus
zwei bis drei nächste Schritte, für die Leitung die Besprechungsvorschläge.

---

## S-11 · Zugriffsliste aktualisieren

**Trägt die gesamte Sichttrennung.** Sammelt die Beteiligten eines Projekts und
verteilt die Liste auf alle Datensätze, die zu diesem Projekt gehören.

Aufgerufen von S-03 (Projektanlage), S-07 (neue Hinweise), vom
Beteiligten-Dialog und vom Nachtlauf.

```
 1  Fehleraufzeichnung setzen [ Ein ]
 2  Fenster fixieren
 3  Variable setzen [ $projektID ; Hole ( Skriptparameter ) ]
 4
 5  # ---- Beteiligtenliste zusammenstellen ----
 6  Variable setzen [ $ids ; AuswerteSQL (
       "SELECT DISTINCT \"_fkPersonID\" FROM \"Projektbeteiligte\" WHERE \"_fkProjektID\" = ?" ;
       "" ; "" ; $projektID ) ]
 7  Gehe zu Layout [ "90 Projekte_Hilfslayout" ]
 8  Suchenmodus aktivieren · Feldwert setzen [ Projekte::__pkProjektID ; $projektID ] · Suchen
 9  Feldwert setzen [ Projekte::s_BeteiligtIDs ; $ids ]
10  Schreibe Änderung Datens. [ ohne Dialog ]
11
12  # ---- Liste auf alle abhängigen Tabellen verteilen ----
13  # Muster je Tabelle, zwölfmal:
14  Gehe zu Layout [ "91 Abschnitte_Hilfslayout" ]
15  Suchenmodus aktivieren · Feldwert setzen [ Abschnitte::_fkProjektID ; $projektID ] · Suchen
16  Feldinhalt ersetzen [ Abschnitte::s_ZugriffIDs ; $ids ; ohne Dialog ]
17  #
18  # dasselbe für: Schritte · Punkte · Ergebnisse · Aufgaben · Dokumente ·
19  #               Dokumentversionen · Risiken · Manuskripte · Einreichungen ·
20  #               Hinweise · Verlauf · Projektbeteiligte
```

> **Warum denormalisiert statt per Bezug?** Zugriffsformeln werden für jeden
> Datensatz einzeln ausgewertet — bei jeder Suche, bei jedem Export. Ein Bezug
> oder ein `AuswerteSQL` an dieser Stelle wäre bei einigen tausend Datensätzen
> spürbar langsam und im Verhalten schwer vorhersagbar. Ein lokales Textfeld ist
> beides nicht.
>
> **Der Preis, offen gesagt:** Wird jemand einem Projekt zugeordnet, greift der
> Zugriff erst, wenn S-11 gelaufen ist. Deshalb ruft der Beteiligten-Dialog das
> Skript unmittelbar auf, und der Nachtlauf wiederholt es für alle Projekte. Wer
> die Zuordnung von Hand in der Tabelle ändert, ohne den Dialog zu benutzen, muss
> S-11 selbst anstoßen — oder bis zum nächsten Morgen warten.

---

## Übersicht der Aufrufe

```
Beim Öffnen ─▶ S-01 Start ─▶ S-02 Meine Projekte laden

Knopf „Neues Projekt" ─▶ S-03 ─┬─▶ S-11 Zugriffsliste
   (nur Stufe Leitung)          ├─▶ S-09 Verlauf
                                └─▶ S-08 ─▶ S-05 ─▶ S-07 ─▶ S-11

Knopf „Abschnitt starten / abschließen" ─▶ S-04 ─┬─▶ S-08 ─▶ S-05 ─▶ S-07
                                                  └─▶ S-09 Verlauf

Knopf „passt" ─▶ S-06
Beteiligte ändern ─▶ S-11

Serverzeitplan nachts  ─▶ S-08 (alle) ─▶ S-05 ─▶ S-07   und   S-11 (alle)
Serverzeitplan montags ─▶ S-10 Wochenimpuls
```

Elf Skripte, davon drei reine Hilfsskripte. Jede Fachlogik existiert genau einmal.
