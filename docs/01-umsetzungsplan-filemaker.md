# Umsetzungsplan: Forschungsprojekt-Management-Tool in FileMaker

Grundlage: [`00-kompendium-fachspezifikation.md`](00-kompendium-fachspezifikation.md)

Dieses Dokument beschreibt **Konzept und Bau-Reihenfolge**. Es ist bewusst kein
vollständiges Feldverzeichnis — die Detailspezifikation entsteht etappenweise
direkt vor der jeweiligen Umsetzung.

---

## 1. Leitgedanke

**Das System begleitet sanft durch zwei Jahre Forschungsarbeit.**

Es richtet sich an zwei Gruppen, die beide wenig Erfahrung mitbringen: an
**Forschende**, die zum ersten Mal ein Projekt durchziehen, und an
**Forschungsleitende**, die zum ersten Mal andere dabei betreuen. Beide brauchen
keinen Wächter, sondern jemanden, der weiß, wie das üblicherweise läuft, und es
im richtigen Moment sagt.

Die Gestaltungsregel durchgehend:

> Das System **weiß**, wie ein Forschungsprojekt verläuft, und **sagt es**.
> Entscheiden tun Menschen.

Daraus folgt:

- **Nichts blockiert.** Keine gesperrten Knöpfe, keine erzwungene Reihenfolge.
- **Alles lässt sich überspringen**, parallel führen oder als „trifft nicht zu"
  markieren.
- **Hinweise statt Fehlermeldungen**, jeder einzeln wegklickbar mit „passt so bei
  uns". Ein Ratgeber, der sich nicht abstellen lässt, wird umgangen — dann
  arbeiten die Leute am System vorbei.
- **Ein Projekt anzulegen kostet einen Titel und ein Startdatum.** Mehr nicht.

Das Kompendium beschreibt in §9 und §10 ein streng regelgeführtes System mit
Pflichtfeldern und erzwungenen Freigaben. Dieser Plan übernimmt dessen
**Fachwissen vollständig**, verwendet es aber als **Inhalt, nicht als Schranke**.

---

## 2. Zwei Stufen

Dasselbe Projekt, zwei Blickwinkel. Die Trennung zieht sich durch die gesamte
Oberfläche und durch die hinterlegten Texte.

### Stufe 1 — Forschende

**Frage:** *Wo stehe ich, und was mache ich als Nächstes?*

Nah dran, ein Projekt im Blick. Sieht den eigenen Stand auf der Zeitachse, die
Checkliste des aktuellen Abschnitts mit Erklärungen, die nächsten zwei bis drei
sinnvollen Schritte. Kein Portfolio, keine Kennzahlen, keine Vergleiche mit
anderen.

### Stufe 2 — Forschungsleitung

**Frage:** *Wie stehen meine Projekte, und wo sollte ich mich einmischen?*

Von oben, mehrere Projekte nebeneinander. Sieht, welche Projekte sich länger
nicht gemeldet haben, wo ein Abschnitt ungewöhnlich lange läuft, und — das ist
der eigentliche Nutzen — **was sich im nächsten Gespräch zu besprechen lohnt**.

Für eine unerfahrene Betreuung ist das der schwierigste Teil: zu wissen, worauf
man bei einem Projekt in Monat 9 überhaupt schauen sollte. Genau dafür hat jeder
Abschnitt einen hinterlegten Betreuungshinweis.

### Was die Stufen unterscheidet

**Forschende sehen ihre eigenen Projekte. Die Forschungsleitung sieht alles.**

| | Forschende (6–9 Personen) | Forschungsleitung (2–3 Personen) |
|---|---|---|
| Eigene Projekte | sehen, bearbeiten | sehen, bearbeiten |
| Fremde Projekte | **nicht sichtbar** | sehen, bearbeiten |
| Gesamtübersicht | nein | ja |
| Besprechungsvorschläge | nein | ja |
| Projekt anlegen, Beteiligte zuordnen | nein | ja |
| Hinweise | Fassung *Forschende* | Fassung *Leitung* |

„Eigenes Projekt" heißt: Es existiert ein Eintrag, der die Person mit dem Projekt
verbindet. Damit vergibt die Leitung Sichtbarkeit.

Die Trennung liegt im **Rechtesystem**, nicht in Suchen oder Anzeigefiltern — sie
greift damit auch beim Export und in selbstgebauten Ansichten.

> **Widerspricht das dem Leitgedanken?** Nein. „Nichts blockiert" gilt dem
> *Arbeitsablauf*: kein Statuswechsel wird verweigert, keine Phase gesperrt, kein
> Abschluss verhindert. Es gilt nicht der Frage, wer wessen Projekt einsieht. Ein
> unerfahrener Forschender, der Bewertungen und Besprechungsnotizen über fremde
> Projekte mitlesen kann, ist kein offenes System, sondern ein unangenehmes.

### Technische Umsetzung

Drei Rechtesets: `Forschende`, `Forschungsleitung` und ein Administrationskonto
für Schema und Pflege der Texte.

Die Sichttrennung trägt ein Textfeld je Datensatz mit der Liste der beteiligten
Personen, verglichen gegen die Personen-ID der angemeldeten Sitzung. Ein Skript
pflegt diese Listen; die Rechtesets werten sie aus.

### Mehrbenutzerbetrieb

Acht bis zwölf gleichzeitige Anwender sind für FileMaker Server eine kleine Last.
Die relevanten Punkte sind nicht Leistung, sondern Verhalten:

- **Datensatzsperren:** Zwei Personen an verschiedenen Checklistenpunkten
  desselben Abschnitts stören einander nicht — das sind verschiedene Datensätze.
  Nur derselbe Datensatz kollidiert, und das ist selten.
- **Über Nacht offen gelassene Datensätze** blockieren den nächtlichen Serverlauf.
  Gegenmittel: Clients nach drei Stunden Leerlauf trennen, plus Fehlerbehandlung
  im Skript, die das betroffene Projekt überspringt statt abzubrechen.
- **Globale Felder gelten je Sitzung** — genau darauf beruht die
  Zugriffstrennung. Umkehrschluss: Gemeinsame Einstellungen liegen in einer
  eigenen Tabelle, niemals in globalen Feldern.

---

## 3. Der Bogen über 24 Monate

### 3.1 Fünf Abschnitte statt zwölf Phasen

Der Vorhabentyp spielt keine Rolle. Statt verschiedener Vorlagen je Studiendesign
gibt es **ein Modell für alles** — grob genug, dass es überall passt, mit feineren
Schritten darunter, die sich einzeln abwählen lassen.

| Abschnitt | Leitfrage | Monate |
|---|---|---|
| **1 Klären** | Was will ich herausfinden, und geht das überhaupt? | 1 – 4 |
| **2 Planen** | Wie gehe ich vor, und darf ich das? | 4 – 9 |
| **3 Sammeln** | Woher kommt mein Material? | 8 – 16 |
| **4 Auswerten** | Was sagen die Daten? | 15 – 19 |
| **5 Veröffentlichen** | Wer soll davon erfahren? | 18 – 24 |

Die **Überlappungen sind Absicht**. In der Realität beginnt das Schreiben während
der Auswertung, und die Literaturarbeit hört nie ganz auf. Die Zeitachse zeigt
deshalb breite, ineinandergreifende Bänder, keine harten Grenzen.

### 3.2 Die feineren Schritte

Unter jedem Abschnitt liegen die Schritte aus §6.1 des Kompendiums. Jeder lässt
sich als **„trifft nicht zu"** markieren und verschwindet dann still.

| Abschnitt | Schritte |
|---|---|
| Klären | Themenidee · Machbarkeit · Literatur und Forschungslücke |
| Planen | Vorgehensplan / Protokoll · Ethik und Datenschutz · Datensetup |
| Sammeln | Material zusammentragen · laufende Qualitätskontrolle |
| Auswerten | Bereinigen und Datenstand festhalten · Analyse |
| Veröffentlichen | Manuskript · Einreichung und Überarbeitung · Abschluss und was ich gelernt habe |

**Das ist die Antwort auf „passt für alles".** Eine systematische Übersichtsarbeit
hakt „Ethik und Datenschutz" in zwei Sekunden als nicht zutreffend ab, eine
prospektive Studie lässt ihn stehen. Beide arbeiten im selben Modell, ohne dass
jemand vorher einen Vorhabentyp auswählen und dessen Konsequenzen verstehen muss.

### 3.3 Ehrlichkeit zur Publikationsphase

Die 24 Monate reichen realistisch bis zur **Einreichung**, nicht bis zur
Veröffentlichung. Nach der Einreichung vergehen typischerweise weitere Monate bis
zur Entscheidung, oft mit einer Überarbeitungsrunde dazwischen.

Das System sagt das offen, statt am Ende Verzug zu melden: Der Zielpunkt von Monat
24 ist „eingereicht". Was danach kommt, läuft ohne Zeitdruck weiter.

Für jemanden, der zum ersten Mal veröffentlicht, ist diese Erwartung selbst schon
eine wichtige Information.

### 3.4 Verschiebung statt Verzug

**Die wichtigste Entscheidung für den sanften Ton.**

Wenn etwas länger dauert, meldet das System keine Überfälligkeit, sondern rechnet
das voraussichtliche Ende neu:

> „Das Sammeln läuft seit zehn Monaten, geplant waren acht. Wenn es so
> weitergeht, wird die Einreichung eher Monat 27 als Monat 22."

Das ist eine Tatsache, kein Vorwurf, und trotzdem die wirksamste Form von Druck —
weil sie die Folge sichtbar macht, statt zu schimpfen. Nach oben verschobene
Termine werden nicht rot markiert; sie stehen einfach da.

Wer den Plan bewusst anpasst, verschiebt den Zielpunkt selbst. Dann ist die neue
Zeitachse die richtige, und das System hört auf zu rechnen.

### 3.5 Der Standort auf der Zeitachse

Jedes Projekt hat ein Startdatum. Daraus ergibt sich jederzeit die Aussage:

> „Monat 9 von 24. Planmäßig wären Sie jetzt im Sammeln — das passt."

oder

> „Monat 9 von 24. Das Sammeln hat noch nicht begonnen. Kein Drama, aber die
> Einreichung rückt entsprechend nach hinten."

Diese eine Zeile steht auf jeder Projektakte ganz oben. Sie ist für Unerfahrene
die wertvollste Einzelinformation im ganzen System, weil sie die Frage beantwortet,
die man sich sonst nicht zu stellen traut: *Bin ich eigentlich im Plan?*

---

## 4. Die drei Schichten

### Schicht 1 — Ablage

Was gibt es, wo steht es. Projekte, Abschnitte, Ergebnisse, Aufgaben, Dokumente,
Personen, Publikationsstand. Frei bearbeitbar.

### Schicht 2 — Orientierung

Was gehört üblicherweise dazu. Zu jedem Abschnitt und jedem Schritt eine
Checkliste mit Erklärung: Wozu ist das da, was sollte am Ende vorliegen, was geht
hier erfahrungsgemäß schief, wie lange dauert es.

Diese Schicht ist der eigentliche Wert. Sie ist Lernmaterial, das zum richtigen
Zeitpunkt erscheint — nicht als Handbuch, das niemand liest.

### Schicht 3 — Aufmerksamkeit

Worauf sollte ich gerade schauen. Weiche Hinweise, **getrennt nach Stufe
formuliert**:

*An Forschende:* „Die Ethikeinreichung steht an. Erfahrungsgemäß dauert das Votum
zwei bis drei Monate — es lohnt sich, das früh loszuschicken."

*An die Forschungsleitung:* „Bei Projekt X steht die Ethikeinreichung an. Ein
gemeinsamer Blick aufs Protokoll vorher spart meist eine Rückfragenrunde."

Schicht 3 ersetzt die Geschäftsregeln aus §10. Gleicher Inhalt, andere Wirkung:
aus „darf nicht" wird „schau mal".

---

## 5. Wie aus Regeln Hinweise werden

| Ursprüngliche Regel (§10) | Umsetzung |
|---|---|
| Keine Phasenaktivierung ohne Entry-Kriterien | Beim Start eines Abschnitts erscheint einmalig, was üblicherweise vorher vorliegt. Startet trotzdem. |
| Kein Abschluss ohne Exit-Kriterien | „Zwei Punkte sind offen — trotzdem abschließen?" Ja ist immer möglich. |
| Keine Datenerhebung ohne Ethikvotum | Notiz auf der Projektakte, sichtbar, wegklickbar wenn nicht ethikpflichtig. |
| Keine finale Analyse ohne Datensatz-Freeze | Hinweis mit kurzer Erklärung, warum ein fester Datenstand vor der Auswertung sinnvoll ist. |
| Kritische Änderungen erzeugen Change Requests | Entfällt im MVP. Protokolländerungen landen in der Dokumenthistorie. |
| Eskalation bei ausbleibendem Statusupdate | Kein Alarm, sondern eine ruhige Sammelmail an die Forschungsleitung. |
| Risiken mit festem Review-Termin | Optionales Datum. Wer keins setzt, wird nicht gemahnt. |

---

## 6. Technische Grundsatzentscheidungen

Nachträglich teuer zu ändern, unabhängig vom Ton des Systems.

### 6.1 Eine Datei
Ohne Patientendaten kein Grund zur Trennung. Eine `.fmp12` hält Schema, Daten und
Oberfläche.

### 6.2 Anchor-Buoy im Beziehungsdiagramm
Pro Kontext ein eigener Anker. Der Graph wird groß, aber lesbar und wartbar.

### 6.3 UUID als Primärschlüssel
`Get(UUID)`, nicht änderbar. Macht Vorlagenimport und spätere Zusammenführung
konfliktfrei. Zusätzlich ein lesbarer Projektcode (`2026-014`) für Menschen.

### 6.4 Namenskonvention
```
__pkProjektID      Primärschlüssel
_fkProjektID       Fremdschlüssel
Status             normales Feld
c_MonatImProjekt   Berechnungsfeld (nicht gespeichert)
s_MonatImProjekt   per Skript gesetzter, gespeicherter Wert
s_ZugriffIDs       trägt die Zugriffstrennung
g_MeineStufe       globales Feld (gilt je Sitzung)
zz_Einstellungen   Hilfstabelle
```

### 6.5 Checklistenpunkte sind Datensätze, keine Textfelder
Jeder Punkt ein eigener Datensatz mit Erklärtext, Erledigt-Kennzeichen,
„trifft nicht zu"-Kennzeichen und Notizfeld.

Der Grund ist nicht mehr die Regelprüfung, sondern: die **Erklärung hängt am
einzelnen Punkt**, und der Fortschritt wird sichtbar. Ein Fließtextfeld kann
weder erklären noch anzeigen, wie weit man ist.

### 6.6 Zeitachse zweigleisig rechnen
Der Standort im Projekt (Monat X von 24) und die Fortschreibung des voraus-
sichtlichen Endes sind die meistgenutzten Werte im System. In der Detailansicht
live gerechnet, für Listen und die Leitungsansicht nächtlich in gespeicherte
`s_`-Felder geschrieben — nicht gespeicherte Berechnungen sind in Listen langsam
und weder sortier- noch durchsuchbar.

### 6.7 Verlaufsprotokoll statt Audit-Log
Ein lesbarer Verlauf je Projekt: „14.03. — Abschnitt Sammeln begonnen (M. Weber)",
„02.04. — Protokoll v3 hochgeladen".

Dient dem Verstehen, nicht der Kontrolle, und ist für neue Beteiligte und für die
Betreuung nützlich. Wird per Script an relevanten Stellen geschrieben, nicht durch
flächendeckende Überwachung. Wo später echte Nachweispflicht entsteht (Ethik,
Publikation), lässt sich gezielt ein strengeres Protokoll ergänzen.

### 6.8 Entwickeln mit dem Data Migration Tool
Ab dem ersten Echtdatensatz nicht mehr an der Live-Datei schrauben:
Entwicklungskopie ändern, `FMDataMigration` überträgt die Produktivdaten, Tausch
im Wartungsfenster.

---

## 7. Datenmodell des MVP

### 7.1 Tabellen

**Stammdaten**
- `Projekte` — Projektakte. Pflicht: Titel und Startdatum. Alles andere optional.
  Die Art des Vorhabens ist ein rein beschreibendes Feld ohne Steuerungswirkung.
- `Personen`, `Rollen`, `Projektbeteiligte` — wer ist mit welcher Rolle beteiligt

**Ablauf**
- `VorlageAbschnitte` — die fünf Abschnitte mit Erklärtexten und Regeldauern
- `VorlageSchritte` — die dreizehn Schritte darunter
- `Abschnitte` — die Abschnitte des konkreten Projekts, mit eigenen Terminen
- `Schritte` — die Schritte des Projekts, abwählbar
- `Punkte` — Checklistenpunkte, Vorlage und Instanz

**Arbeitsebene**
- `Ergebnisse` — was am Ende eines Abschnitts vorliegen sollte
- `Aufgaben` — an Abschnitt, Schritt, Ergebnis oder direkt am Projekt

**Nachweise und Steuerung**
- `Dokumente` + `Dokumentversionen`
- `Risiken` — was gerade hakt
- `Manuskripte` + `Einreichungen` — Publikationsstand
- `Hinweise` — erzeugte Hinweise, mit Stufe und „weggeklickt"-Kennzeichen
- `Verlauf` — das Verlaufsprotokoll
- `zz_Einstellungen` — gemeinsame Einstellungen

Kein Tabelle für Vorhabentypen — es gibt nur ein Ablaufmodell.

### 7.2 Kernbeziehungen

```
Projekte ──< Abschnitte ──< Schritte ──< Punkte
    │             └──< Ergebnisse ──< Aufgaben
    ├──< Projektbeteiligte >── Personen >── Rollen
    ├──< Dokumente ──< Dokumentversionen
    ├──< Risiken
    ├──< Manuskripte ──< Einreichungen
    ├──< Hinweise
    └──< Verlauf
```

Aufgaben hängen an einem Abschnitt, einem Schritt oder direkt am Projekt — alle
drei erlaubt. Wer ohne Struktur arbeiten will, kann das.

### 7.3 Projektanlage

Titel und Startdatum eingeben. Das System legt daraufhin die fünf Abschnitte mit
den aus dem Startdatum berechneten Regelterminen an, dazu die dreizehn Schritte
und ihre Checklisten.

Der Vorschlag erscheint zur Bestätigung, nicht als vollendete Tatsache: Termine
lassen sich verschieben, Schritte abwählen, Abschnitte ergänzen. Wer alles
ablehnt, bekommt ein leeres Projekt — auch das ist ein gültiger Zustand.

---

## 8. Die Wissensschicht

Der Teil, der das System wertvoll macht, und der die meiste inhaltliche Arbeit
kostet. Pro Abschnitt und pro Schritt hinterlegt:

- **Wozu das da ist** — zwei, drei Sätze
- **Was am Ende vorliegen sollte** — die Checkliste, jeder Punkt einzeln erklärt
- **Typische Stolpersteine** — was hier erfahrungsgemäß schiefgeht
- **Regeldauer** — als Orientierung, Grundlage der Zeitachse
- **Für Forschende** — was jetzt sinnvollerweise als Nächstes ansteht
- **Für die Forschungsleitung** — worauf zu schauen ist und was sich fürs
  nächste Gespräch lohnt

Die letzten beiden Punkte sind die Umsetzung der zwei Stufen. Derselbe
Sachverhalt, zweimal formuliert.

Diese Inhalte liegen als **Daten** in `VorlageAbschnitte` und `VorlageSchritte`,
nicht im Programmcode. Sie sind ohne Entwicklungsaufwand pflegbar und wachsen mit
der eigenen Erfahrung.

---

## 9. Oberfläche

Nach der Anmeldung entscheidet das Rechteset über die Einstiegsseite.

### Einstieg Forschende — „Mein Projekt"

Oben die Standortzeile („Monat 9 von 24 — planmäßig im Sammeln"). Darunter der
aktuelle Abschnitt mit seiner Checkliste und den Erklärtexten. Rechts eine
schmale Spalte mit zwei bis drei Hinweisen, jeder wegklickbar. Unten die eigenen
offenen Aufgaben.

Bei mehreren eigenen Projekten eine schlichte Auswahl davor.

### Einstieg Forschungsleitung — „Meine Projekte"

Alle betreuten Projekte als ruhige Liste: Titel, wer daran arbeitet, Standort auf
der Zeitachse, Kennzeichnung *läuft · stockt · ruht · abgeschlossen*, und die
voraussichtliche Einreichung.

Darunter der eigentliche Kern: **„Was sich zu besprechen lohnt"** — je Projekt ein
bis zwei Sätze aus der Wissensschicht, passend zum aktuellen Stand.

### Gemeinsame Ansichten

- **Projektakte** — Kopfbereich mit Zeitachse, darunter Reiter für Abschnitte,
  Ergebnisse, Aufgaben, Dokumente, Risiken, Publikation, Verlauf
- **Abschnittsansicht** — links Checkliste mit sichtbaren Erklärtexten, rechts
  Ergebnisse und Aufgaben
- **Zeitachse** — die fünf Bänder über 24 Monate, Soll und Ist übereinander

Grundton aller Texte: sachlich, knapp, ohne Ausrufezeichen. „Läuft seit zehn
Monaten" statt „ÜBERFÄLLIG!".

---

## 10. Automationen

Ein nächtlicher Serverlauf: Standort und voraussichtliches Ende neu berechnen,
Hinweise erzeugen und veraltete zurückziehen, Verlauf verdichten.

Dazu **ein Wochenimpuls pro Person**, abschaltbar:

- *An Forschende:* wo Sie stehen, plus zwei bis drei konkrete nächste Schritte
- *An die Forschungsleitung:* was sich diese Woche zu besprechen lohnt

Eine ruhige Mail pro Woche statt Einzelbenachrichtigungen bei jedem Ereignis.

Rechenintensives (Projektanlage, Leitungsansicht) läuft über *Perform Script on
Server*.

---

## 11. Bau-Reihenfolge

### Etappe 0 — Fundament
Datei, Namenskonvention, Hilfstabelle, Konten und die drei Rechtesets samt
Zugriffstrennung, Serverablage, Sicherungsplan.
*Ergebnis:* Datei liegt auf dem Server, Anmeldung funktioniert.

### Etappe 1 — Projekte und Personen
`Projekte`, `Personen`, `Rollen`, `Projektbeteiligte`, `Verlauf`. Projektakte mit
Kopfbereich und Beteiligten. Schnellanlage: Titel und Startdatum, fertig.
*Ergebnis:* Projekte lassen sich anlegen und Beteiligte zuordnen.

### Etappe 2 — Abschnitte, Schritte und Zeitachse
`VorlageAbschnitte`, `VorlageSchritte`, `Abschnitte`, `Schritte`, `Punkte`. Die
fünf Abschnitte und dreizehn Schritte als Vorlage, Terminberechnung aus dem
Startdatum, Standortzeile, Fortschreibung des voraussichtlichen Endes.
*Ergebnis:* Ein neues Projekt bekommt seinen 24-Monats-Bogen; jede Projektakte
sagt, in welchem Monat man ist und wohin es läuft.

### Etappe 3 — Wissensschicht *(inhaltliches Kernstück)*
Erklärtexte, Checklistenpunkte, Stolpersteine und Regeldauern für alle Abschnitte
und Schritte einpflegen, jeweils in beiden Fassungen (Forschende /
Forschungsleitung). Anzeige in der Abschnittsansicht.
*Ergebnis:* Wer einen Abschnitt öffnet, sieht ohne Nachfragen, worum es geht.
Überwiegend Schreibarbeit, kaum Entwicklung.

### Etappe 4 — Die zwei Einstiegsseiten
„Mein Projekt" und „Meine Projekte" inklusive der Besprechungsvorschläge.
*Ergebnis:* Beide Stufen haben ihren eigenen Zugang. Ab hier ist das System für
beide Zielgruppen sinnvoll benutzbar.

### Etappe 5 — Ergebnisse und Aufgaben
`Ergebnisse`, `Aufgaben`, Aufgabenliste.
*Ergebnis:* Aufgaben sind zuweisbar, Fortschritt wird sichtbar.

### Etappe 6 — Dokumente
`Dokumente` + `Dokumentversionen`, Ablage mit externer Containersicherung, Historie.
*Ergebnis:* Eine neue Version ergänzt die alte, statt sie zu ersetzen.

### Etappe 7 — Risiken und Blocker
`Risiken`, schlank: was hakt, wer kümmert sich, seit wann.
*Ergebnis:* Stockende Projekte sind in der Leitungsansicht erkennbar.

### Etappe 8 — Hinweis-Schicht
`Hinweise`, die Regeln aus Kapitel 5, Wegklick-Mechanik, Hinweisspalte.
*Ergebnis:* Das System meldet sich von selbst — und lässt sich beruhigen.

### Etappe 9 — Publikationsstand
`Manuskripte`, `Einreichungen` mit den Statuswerten aus §9.11, ohne Zeitdruck
jenseits von Monat 24.
*Ergebnis:* Der Weg von Draft bis Published ist abbildbar.

### Etappe 10 — Serverlauf und Wochenimpuls
Nachtlauf, Wochenmail in beiden Fassungen.
*Ergebnis:* Das System läuft ohne Eingriff und meldet sich von selbst.

### Etappe 11 — Feinschliff und Einführung
Texte überarbeiten, Export nach Excel und PDF, Testlauf mit zwei echten
Projekten, kurze Anleitung.
*Ergebnis:* produktiv nutzbar.

Die Reihenfolge ist so gewählt, dass nach **Etappe 4** der Kernnutzen steht —
Orientierung auf der Zeitachse für beide Stufen — auch wenn Aufgaben, Dokumente
und Publikation noch fehlen.

---

## 12. Arbeitsweise

`.fmp12`-Dateien sind Binärformat und entstehen nur in FileMaker Pro selbst.

**Im Repository entsteht pro Etappe:** Detailspezifikation mit Feldliste und
Typen, Berechnungsformeln zum Kopieren, Script-Schritte in ausführbarer
Reihenfolge, Layout-Aufbau, Import-fertige CSV-Dateien für Vorlagen, Wertelisten
und Wissensschicht.

**In FileMaker Pro passiert:** der Nachbau anhand dieser Anleitung.

---

## 13. Offene Punkte

**Vor Etappe 2:**
1. Passen die Regeldauern der fünf Abschnitte (4 / 5 / 8 / 4 / 6 Monate mit
   Überlappung), oder gibt es eigene Erfahrungswerte?
2. Ist Monat 24 der Zielpunkt für die **Einreichung** (so vorgeschlagen) oder für
   die Annahme?

**Vor Etappe 3:**
3. Wer schreibt die Erklärtexte und Stolpersteine? Ich lege Entwürfe für alle
   Abschnitte und Schritte in beiden Fassungen vor; die fachliche Prüfung und der
   eigene Erfahrungsschatz müssen von Ihnen kommen. Das ist der Teil, der das
   System vom Lehrbuch unterscheidet.

**Vor Etappe 8 und 10:**
4. Ab wann gilt ein Projekt als „meldet sich länger nicht" — vier Wochen, acht?
5. Steht ein SMTP-Zugang für den Wochenimpuls bereit?
6. Dokumente in FileMaker-Containern oder nur Verweise auf einen bestehenden
   Netzwerkspeicher?

**Technisch:**
7. Welche FileMaker-Serverversion steht bereit, wie viele gleichzeitige Nutzer?
