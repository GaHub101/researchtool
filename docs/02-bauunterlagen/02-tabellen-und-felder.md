# Tabellen und Felder

**20 Tabellen, rund 260 Felder.** Alle Bezeichner deutsch. Alles, was nicht mit ⊕
markiert ist, entsteht automatisch beim Import der Dateien aus `seed/` — dort
steht dann nur der Typ auf *Text* und muss korrigiert werden.

## Legende

| Zeichen | Bedeutung |
|---|---|
| **T** | Text · **Z** Zahl · **D** Datum · **ZS** Zeitstempel · **C** Container |
| ⊕ | **Von Hand anlegen** — Berechnung, Container oder globales Feld. Import kann das nicht. |
| ★ | Pflichtfeld im fachlichen Sinn — aber ohne technische Sperre (Leitgedanke) |
| 🔒 | Trägt die Zugriffstrennung — siehe [`07-rechte-und-mehrbenutzer.md`](07-rechte-und-mehrbenutzer.md) |
| F-nn | Formel steht in [`04-formeln.md`](04-formeln.md) |
| WL-nn | Werteliste, Abschnitt 21 dieses Dokuments |

**Standardoptionen**, überall gleich und im Folgenden nicht wiederholt:

- Jedes `__pk…` — Text, Automatisch eingeben ▸ Berechneter Wert `Hole ( UUID )`,
  Haken bei *Feldinhalt darf nicht geändert werden*
- Jedes `ErstelltAm` — Zeitstempel, Automatisch ▸ Erstellungsdatum
- Jedes `ErstelltVon` — Text, Automatisch ▸ Erstellung Kontoname
- Jedes `GeaendertAm` / `GeaendertVon` — analog mit Änderungsdatum
- Jedes `s_ZugriffIDs` 🔒 — Text, wird von Skript S-11 geschrieben

---

## 1. Projekte — die Projektakte

*Herkunft: Bildschirme 01, 02, 03, 05*

| Feld | Typ | Optionen / Formel |
|---|---|---|
| `__pkProjektID` | T | Standard |
| `LfdNr` | Z | Automatisch ▸ Seriennummer, ab 1 |
| `Titel` | T ★ | |
| `Startdatum` | D ★ | Automatisch ▸ Erstellungsdatum (überschreibbar) |
| `Kurzbeschreibung` | T | |
| `Fragestellung` | T | |
| `ArtDesVorhabens` | T | WL-01 — **rein beschreibend, steuert nichts** |
| `Fachgebiet` | T | |
| `Status` | T | WL-02, Vorgabe `aktiv` |
| `BogenLaengeMonate` | Z | Vorgabe 24 |
| `ZielpunktEinreichungMonat` | Z | Vorgabe 22 |
| `Ethikpflicht` | T | WL-03, Vorgabe `unklar` |
| `EthikAktenzeichen` | T | |
| `EthikEinreichung` `EthikVotum` | D | |
| `EthikGeltungsbereich` | T | |
| `Notiz` | T | |
| `ErstelltAm` `ErstelltVon` `GeaendertAm` `GeaendertVon` | ZS/T | Standard |

**Zugriffsteuerung** (Skript S-11):

| Feld | Typ | Zweck |
|---|---|---|
| `s_BeteiligtIDs` 🔒 | T | Zeilenweise Liste aller `__pkPersonID` der Beteiligten. **Das Feld, an dem die gesamte Sichttrennung hängt.** |

**Nachts geschriebene Werte** (Skript S-08) — sie machen die Leitungsliste schnell
und sortierbar:

| Feld | Typ |
|---|---|
| `s_AktuellerAbschnittNr` | Z |
| `s_AktuellerAbschnittName` | T |
| `s_AktuellerAbschnittSchwerpunktBis` | Z |
| `s_AktuelleAbschnittID` | T |
| `s_LetzteAktivitaet` | D |
| `s_MonatImProjekt` `s_Verzug` | Z |
| `s_EinreichungVoraussichtlich` | D |
| `s_Zustand` | T |
| `s_PunkteOffen` `s_PunkteGesamt` | Z |

**Von Hand anzulegen:**

| Feld | Typ | Formel |
|---|---|---|
| `c_Projektcode` | ⊕ T | F-01 |
| `c_MonatImProjekt` | ⊕ Z | F-02 |
| `c_SollAbschnittNr` | ⊕ Z | F-03 |
| `c_SollAbschnittName` | ⊕ T | F-04 |
| `c_Standortsatz` | ⊕ T | F-05 |
| `c_ZeitfortschrittProzent` | ⊕ Z | F-06 |
| `c_Verzug` | ⊕ Z | F-07 |
| `c_EinreichungVoraussichtlich` | ⊕ D | F-08 |
| `c_EinreichungText` | ⊕ T | F-09 — „März 2028" |
| `c_Zustand` | ⊕ T | F-10 |
| `c_HeutePx` | ⊕ Z | F-16 |
| `k_Eins` | ⊕ Z | F-19 — **gespeichert!** Haken bei *Nicht speichern* entfernen |
| `g_MeineStufe` | ⊕ T | global |
| `g_MeinePersonID` 🔒 | ⊕ T | global |
| `g_MeinKonto` | ⊕ T | global |
| `g_Eingabe1` | ⊕ T | global, für Dialoge |

> Die globalen Felder werden beim Start von Skript S-01 gefüllt. Sie liegen
> bewusst in `Projekte` und nicht in `zz_Einstellungen` — globale Felder sind in
> FileMaker aus jedem Kontext heraus lesbar, auch ohne Beziehung.
>
> **Wichtig für den Mehrbenutzerbetrieb:** Globale Felder gelten **je Sitzung**.
> Jede angemeldete Person hat ihre eigenen Werte — genau deshalb funktioniert die
> Zugriffstrennung über `g_MeinePersonID`. Gemeinsame Einstellungen dürfen
> deshalb **niemals** in globalen Feldern liegen; dafür gibt es
> `zz_Einstellungen`.

---

## 2. Personen

| Feld | Typ | Optionen |
|---|---|---|
| `__pkPersonID` | T | Standard |
| `Nachname` `Vorname` | T | |
| `Kontoname` | T | **verbindet die Person mit ihrem FileMaker-Konto** — muss exakt stimmen |
| `Mail` | T | für den Wochenimpuls |
| `Stufe` | T | WL-11 |
| `Aktiv` | Z | Vorgabe 1 |
| `c_Name` | ⊕ T | F-17 |
| `c_Kurzname` | ⊕ T | F-17 — „M. Weber" |

Erwartete Größe: 6–9 Forschende, 2–3 Forschungsleitende.

## 3. Rollen

`__pkRolleID` T · `Bezeichnung` T · `Reihenfolge` Z

Startdaten in `seed/Rollen.tab`: PI / Betreuung · Projektführung · Datenerhebung ·
Statistik · Regulatorik · Co-Autorenschaft · Koordination

## 4. Projektbeteiligte

`__pkBeteiligtID` T · `_fkProjektID` T · `_fkPersonID` T · `_fkRolleID` T ·
`RACI` T (WL-13) · `Hauptverantwortlich` Z · `ErstelltAm` ZS · `s_ZugriffIDs` 🔒 T

> **Diese Tabelle entscheidet, wer was sieht.** Ein Eintrag hier verschafft der
> Person Zugriff auf das gesamte Projekt; kein Eintrag bedeutet für Forschende:
> Projekt nicht sichtbar. Änderungen müssen deshalb Skript S-11 auslösen.

---

## 5. VorlageAbschnitte — Wissensschicht, Abschnittsebene

*Wird komplett per Import gefüllt.*

| Feld | Typ |
|---|---|
| `Nr` | Z |
| `Name` `Leitfrage` | T |
| `Band_Von_Monat` `Band_Bis_Monat` | Z |
| `Schwerpunkt_Von_Monat` `Schwerpunkt_Bis_Monat` | Z |
| `Zweck` `Stolpersteine` | T |
| `Hinweis_Forschende` `Hinweis_Leitung` | T |

> **Warum zwei Monatsbereiche?** `Band_…` sind die überlappenden Balken der
> Zeitachse (Klären 1–4, Planen 4–9, Sammeln 8–16 …). `Schwerpunkt_…` sind die
> überschneidungsfreien Bereiche (1–4, 5–8, 9–15, 16–19, 20–24), aus denen sich
> eindeutig ableiten lässt, in welchem Abschnitt man planmäßig sein *sollte*.
> Ohne diese Trennung wäre der Standortsatz mehrdeutig.

## 6. VorlageSchritte

`AbschnittNr` Z · `Nr` Z · `Name` T · `Zweck` T · `Stolpersteine` T ·
`Hinweis_Forschende` T · `Hinweis_Leitung` T

## 7. VorlagePunkte

`AbschnittNr` Z · `SchrittNr` Z · `Nr` Z · `Punkt` T · `Erklaerung` T

Die drei Vorlagentabellen brauchen **kein** Primärschlüsselfeld — die Beziehungen
B-30 und B-31 laufen über die Nummern.

---

## 8. Abschnitte — die fünf Abschnitte des konkreten Projekts

*Herkunft: Bildschirme 03, 04, 05*

| Feld | Typ | Optionen |
|---|---|---|
| `__pkAbschnittID` | T | Standard |
| `_fkProjektID` | T | |
| `Nr` | Z | |
| `Name` `Leitfrage` | T | aus Vorlage kopiert |
| `Band_Von_Monat` `Band_Bis_Monat` | Z | aus Vorlage |
| `Schwerpunkt_Von_Monat` `Schwerpunkt_Bis_Monat` | Z | aus Vorlage |
| `Soll_Start` `Soll_Ende` | D | aus Startdatum berechnet (S-03) |
| `Ist_Start` `Ist_Ende` | D | |
| `Status` | T | WL-04, Vorgabe `offen` |
| `Zweck` `Stolpersteine` `Hinweis_Forschende` `Hinweis_Leitung` | T | aus Vorlage kopiert |
| `Notiz` | T | |
| `s_ZugriffIDs` 🔒 | T | |
| `c_PunkteGesamt` `c_PunkteErledigt` | ⊕ Z | F-12, F-13 |
| `c_BandGrafik` `c_IstGrafik` | ⊕ T | F-22, F-23 |
| `c_BandStartPx` `c_BandBreitePx` `c_IstStartPx` `c_IstBreitePx` | ⊕ Z | F-14, F-15 — nur für die grafische Zeitachse |

> **Warum werden die Texte kopiert und nicht per Bezug gelesen?** Damit eine
> spätere Verbesserung der Wissensschicht laufende Projekte nicht rückwirkend
> verändert — und damit die Abschnittsansicht ohne Bezug zur Vorlagentabelle
> auskommt.

## 9. Schritte

`__pkSchrittID` T · `_fkAbschnittID` T · `_fkProjektID` T · `Nr` Z · `Name` T ·
`Zweck` T · `Stolpersteine` T · `Hinweis_Forschende` T · `Hinweis_Leitung` T ·
`Status` T (WL-05) · `TrifftNichtZu` Z (Vorgabe 0) · `Ist_Ende` D ·
`s_ZugriffIDs` 🔒 T · `c_PunkteGesamt` ⊕Z (F-12) · `c_PunkteErledigt` ⊕Z (F-13)

> `_fkProjektID` ist bewusst redundant. Es erspart auf Bildschirm 01 eine
> Bezugsebene und trägt zugleich die Zugriffstrennung. Preis: Skript S-03 muss es
> mitschreiben — drei Zeilen.

## 10. Punkte — die Checklistenpunkte

| Feld | Typ | Optionen |
|---|---|---|
| `__pkPunktID` | T | Standard |
| `_fkSchrittID` `_fkAbschnittID` `_fkProjektID` | T | |
| `SchrittNr` | Z | denormalisiert — Sortierschlüssel des Portals |
| `Nr` | Z | 0 bei Überschriftszeilen |
| `Punkt` | T | bei Überschriftszeilen der Schrittname |
| `Erklaerung` | T | |
| `Erledigt` | Z | Vorgabe 0, Kontrollkästchen |
| `Erledigt_am` | D | |
| `Erledigt_von` | T | |
| `TrifftNichtZu` | Z | Vorgabe 0 |
| `IstUeberschrift` | Z | Vorgabe 0 — siehe Kasten |
| `Notiz` | T | |
| `s_ZugriffIDs` 🔒 | T | |
| `IstOffen` `IstGezaehlt` | ⊕ Z | F-11 — **gespeichert** |

> **`IstUeberschrift` — Ersatz für verschachtelte Portale.** FileMaker kann kein
> Portal in einem Portal darstellen. Damit die Checkliste trotzdem nach Schritten
> gegliedert erscheint, legt Skript S-03 je Schritt eine zusätzliche Zeile mit
> `Nr = 0`, `IstUeberschrift = 1` und dem Schrittnamen in `Punkt` an. Nach
> `SchrittNr, Nr` sortiert steht sie automatisch über ihrer Gruppe; im Layout wird
> das Kontrollkästchen dort ausgeblendet und der Text fett gesetzt. Ein Feld und
> drei Skriptzeilen statt eines fehlenden Programmfeatures.

---

## 11. Ergebnisse

`__pkErgebnisID` T · `_fkProjektID` T · `_fkAbschnittID` T · `Titel` T ·
`Typ` T · `Verantwortlich` T · `Pruefer` T · `Faellig` D · `Status` T (WL-06) ·
`Version` T · `Freigabedatum` D · `Notiz` T · `ErstelltAm` ZS · `s_ZugriffIDs` 🔒 T

## 12. Aufgaben

`__pkAufgabeID` T · `_fkProjektID` T · `_fkAbschnittID` T · `_fkSchrittID` T ·
`_fkErgebnisID` T · `Titel` T · `Beschreibung` T · `_fkPersonID` T ·
`Termin` D · `Prioritaet` T (WL-08) · `Status` T (WL-07, Vorgabe `offen`) ·
`Aufwand_geschaetzt` Z · `Aufwand_tatsaechlich` Z · `ErstelltAm` ZS ·
`ErstelltVon` T · `s_ZugriffIDs` 🔒 T · `c_IstOffen` ⊕Z (F-18)

> Alle vier Fremdschlüssel dürfen leer sein — bis auf `_fkProjektID`, das die
> Zugriffstrennung trägt. Eine Aufgabe kann an einem Schritt, an einem Abschnitt,
> an einem Ergebnis oder direkt am Projekt hängen.

## 13. Dokumente

`__pkDokumentID` T · `_fkProjektID` T · `_fkAbschnittID` T · `_fkErgebnisID` T ·
`Titel` T · `Dokumenttyp` T (WL-12) · `AktuelleVersion` T · `Status` T (WL-06) ·
`Notiz` T · `ErstelltAm` ZS · `ErstelltVon` T · `s_ZugriffIDs` 🔒 T

## 14. Dokumentversionen

`__pkVersionID` T · `_fkDokumentID` T · `_fkProjektID` T · `Version` T ·
`Datei` ⊕**C** · `Bemerkung` T · `HochgeladenAm` ZS · `HochgeladenVon` T ·
`s_ZugriffIDs` 🔒 T

> `Datei` ist ein Containerfeld und muss von Hand angelegt werden. In den Optionen
> **Speicherung ▸ Container extern sichern** einschalten — sonst wächst die
> Datei ins Unhandliche und die Sicherung dauert ewig.

## 15. Risiken

`__pkRisikoID` T · `_fkProjektID` T · `_fkAbschnittID` T · `Kategorie` T (WL-09) ·
`Beschreibung` T · `Ursache` T · `Wahrscheinlichkeit` T (WL-14) ·
`Auswirkung` T (WL-14) · `Gegenmassnahme` T · `_fkPersonID` T · `PruefDatum` D ·
`Status` T (WL-15) · `ErstelltAm` ZS · `s_ZugriffIDs` 🔒 T

## 16. Manuskripte

`__pkManuskriptID` T · `_fkProjektID` T · `Arbeitstitel` T · `Zieljournal` T ·
`Alternativjournale` T · `Autorenreihenfolge` T · `Status` T (WL-10) ·
`Abschnittsstatus` T · `Notiz` T · `ErstelltAm` ZS · `s_ZugriffIDs` 🔒 T

## 17. Einreichungen

`__pkEinreichungID` T · `_fkManuskriptID` T · `_fkProjektID` T · `Journal` T ·
`Runde` Z · `Eingereicht_am` D · `Entscheidung` T (WL-10) · `Entscheidung_am` D ·
`Gutachterkommentare` T · `Antwortschreiben` T · `s_ZugriffIDs` 🔒 T

---

## 18. Hinweise — die Hinweisschicht

*Herkunft: Bildschirm 01 (Spalte rechts), Bildschirm 02 (unterer Bereich)*

| Feld | Typ | Optionen |
|---|---|---|
| `__pkHinweisID` | T | Standard |
| `_fkProjektID` | T | |
| `Schluessel` | T | Kennung der Regel, z. B. `ETHIK_FEHLT` — verhindert Doppelanlage |
| `Stufe` | T | WL-11 — **derselbe Sachverhalt existiert zweimal, einmal je Stufe** |
| `Titel` `Text` | T | |
| `Rang` | Z | 1 = zuerst zeigen |
| `Weggeklickt` | Z | Vorgabe 0 |
| `Weggeklickt_am` | D | |
| `Weggeklickt_von` | T | |
| `ErzeugtAm` | ZS | |
| `Zurueckgezogen` | Z | Vorgabe 0 — Regel trifft nicht mehr zu |
| `s_ZugriffIDs` 🔒 | T | |

> `Schluessel` + `_fkProjektID` + `Stufe` sind zusammen eindeutig. Skript S-07
> prüft darauf, bevor es anlegt. Ein weggeklickter Hinweis wird nie wieder
> erzeugt — das ist die technische Umsetzung von „passt so bei uns".
>
> **Getrennt je Stufe, mit Absicht:** Die Forschungsleitung sieht nicht, was eine
> forschende Person weggeklickt hat, und umgekehrt.

## 19. Verlauf

`__pkVerlaufID` T · `_fkProjektID` T · `Zeitstempel` ZS (Auto Erstellung) ·
`Konto` T (Auto Erstellung Kontoname) · `Bereich` T · `Text` T · `Bezug_ID` T ·
`s_ZugriffIDs` 🔒 T

> Bewusst schlank: ein lesbarer Satz je Ereignis, kein Feld-für-Feld-Protokoll.
> Beschrieben von Skript S-09, aufgerufen aus allen anderen Skripten.

## 20. zz_Einstellungen

Genau **ein** Datensatz. Nach dem Import einmal anlegen und füllen. Von allen
lesbar, nur von der Administration änderbar.

`__pkEinstellungID` T · `StilleFristTage` Z (Vorgabe 42) · `RuhtFristTage` Z
(Vorgabe 120) · `WochenimpulsAktiv` Z (Vorgabe 1) · `AbsenderMail` T ·
`SMTPServer` T · `ZeitachseBreitePx` Z (Vorgabe 600) · `Version` T

---

## 21. Wertelisten

Alle als *Benutzerdefinierte Werte*.

| Nr | Name | Werte |
|---|---|---|
| WL-01 | `Art des Vorhabens` | Retrospektive Auswertung · Prospektive Studie · Fallserie · Systematische Übersichtsarbeit · Qualifikationsarbeit · Methodenarbeit · Sonstiges |
| WL-02 | `Projektstatus` | aktiv · ruht · abgeschlossen · abgebrochen |
| WL-03 | `Ethikpflicht` | ja · nein · unklar |
| WL-04 | `Abschnittsstatus` | offen · läuft · abgeschlossen · übersprungen |
| WL-05 | `Schrittstatus` | offen · läuft · erledigt |
| WL-06 | `Ergebnisstatus` | offen · in Arbeit · in Prüfung · freigegeben |
| WL-07 | `Aufgabenstatus` | offen · in Arbeit · erledigt |
| WL-08 | `Priorität` | hoch · mittel · niedrig |
| WL-09 | `Risikokategorie` | Ethik · Datenzugang · Datenqualität · Ressourcen · Statistik · Co-Autoren · Einreichung · Sonstiges |
| WL-10 | `Publikationsstatus` | Entwurf · Internes Review · Eingereicht · Kleine Überarbeitung · Große Überarbeitung · Neu eingereicht · Angenommen · Abgelehnt · Veröffentlicht |
| WL-11 | `Stufe` | Forschende · Leitung |
| WL-12 | `Dokumenttyp` | Protokoll · Ethikunterlagen · Erhebungsbogen · Datensatz · Auswertung · Manuskript · Korrespondenz · Sonstiges |
| WL-13 | `RACI` | Verantwortlich · Rechenschaftspflichtig · Beratend · Informiert |
| WL-14 | `Einschätzung` | hoch · mittel · niedrig |
| WL-15 | `Risikostatus` | offen · in Bearbeitung · entschärft · eingetreten · geschlossen |

**Kein Statusfeld ist gesperrt.** Alle Wertelisten sind als Aufklappliste
zugewiesen und frei wählbar — die Skripte setzen sie zusätzlich, verhindern aber
nichts.

---

## 22. Nach dem Import: Typen korrigieren

Der Import legt **alle** Felder als Text an. Zu korrigieren sind nur:

**Auf Zahl:** alle `Nr`, `LfdNr`, `AbschnittNr`, `SchrittNr`, `Runde`, `Rang`,
`*_Monat`, `*Monate`, `*Tage`, `*Px`, `Erledigt`, `TrifftNichtZu`,
`IstUeberschrift`, `Weggeklickt`, `Zurueckgezogen`, `Aktiv`,
`Hauptverantwortlich`, `WochenimpulsAktiv`, `Aufwand_*`, `Reihenfolge`,
`s_MonatImProjekt`, `s_Verzug`, `s_PunkteOffen`, `s_PunkteGesamt`,
`s_AktuellerAbschnittNr`, `s_AktuellerAbschnittSchwerpunktBis`

**Auf Datum:** `Startdatum`, alle `Soll_*`, `Ist_*`, `Faellig`, `Termin`,
`PruefDatum`, `Freigabedatum`, `Eingereicht_am`, `Entscheidung_am`,
`Erledigt_am`, `Weggeklickt_am`, `EthikEinreichung`, `EthikVotum`,
`s_LetzteAktivitaet`, `s_EinreichungVoraussichtlich`

**Auf Zeitstempel:** alle `*Am` außer `Erledigt_am` und `Weggeklickt_am`

Alles andere bleibt Text — auch `s_BeteiligtIDs` und alle `s_ZugriffIDs`.

> Schneller Weg: In *Datei ▸ Verwalten ▸ Datenbank ▸ Felder* nach Namensteil
> sortieren, mehrere Felder markieren, Typ in einem Rutsch ändern.
