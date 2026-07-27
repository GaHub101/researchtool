# Rechte, Sichttrennung und Mehrbenutzerbetrieb

Erwartete Nutzung: **6–9 Forschende, 2–3 Forschungsleitende**, alle gleichzeitig
an einer Datei auf FileMaker Server.

---

## 1 · Die Grundregel

> **Forschende sehen ihre eigenen Projekte. Die Forschungsleitung sieht alles.**

Das ist kein Nebenaspekt der Oberfläche, sondern eine Eigenschaft der Datenbank.
Die Trennung liegt im **Rechtesystem**, nicht in Suchen oder Portalfiltern — sie
greift damit auch beim Export, in der Suche, in selbstgebauten Ansichten und über
die Datenschnittstelle.

Was das konkret bedeutet:

| | Forschende | Forschungsleitung |
|---|---|---|
| Eigene Projekte | sehen, bearbeiten | sehen, bearbeiten |
| Fremde Projekte | **nicht sichtbar** | sehen, bearbeiten |
| Übersicht aller Projekte | nein | ja — Layout `02` |
| Besprechungsvorschläge | nein | ja |
| Hinweise | nur Fassung *Forschende* | nur Fassung *Leitung* |
| Neues Projekt anlegen | nein | ja |
| Personen und Beteiligte pflegen | nein | ja |
| Wissensschicht ändern | nein | nein (nur Administration) |

„Eigenes Projekt" heißt: Es existiert ein Eintrag in `Projektbeteiligte`, der die
Person mit dem Projekt verbindet. Diese Tabelle ist damit der Schalter, mit dem
die Leitung Sichtbarkeit vergibt.

> **Warum diese Trennung, obwohl das System sonst nichts verbietet?** Der
> Leitgedanke „nichts blockiert" gilt dem *Arbeitsablauf* — kein Statuswechsel
> wird verweigert, keine Phase gesperrt. Er gilt nicht der Frage, wer wessen
> Projekt einsieht. Ein unerfahrener Forschender, der die Bewertungen und
> Besprechungsnotizen über fremde Projekte lesen kann, ist kein offenes System,
> sondern ein unangenehmes.

---

## 2 · Rechtesets

Drei Stück. *Datei ▸ Verwalten ▸ Sicherheit ▸ Erweiterte Einstellungen ▸
Berechtigungen*.

| Rechteset | Wer | Datensätze | Layouts | Skripte |
|---|---|---|---|---|
| `Forschende` | 6–9 Personen | **eingeschränkt**, siehe §3 | alle einsehbar außer `02`, `02b` | alle außer S-03 |
| `Forschungsleitung` | 2–3 Personen | alle, ändern erlaubt | alle | alle |
| `Administration` | 1–2 Personen | alle | alle | alle, dazu Schema und Wissensschicht |

**Erweiterte Rechte** bei allen dreien: `fmapp` (FileMaker-Netzwerkzugriff).
Bei `Administration` zusätzlich `fmreauthenticate`.

**Wichtig:** Die Namen der Rechtesets sind fachlich bedeutsam — Skript S-01
Zeile 7 leitet daraus die Stufe ab. Wer sie umbenennt, muss dort nachziehen.

### Layoutrechte für `Forschende`

Layout `02 Meine Projekte` und `02b Besprechungsliste` auf **kein Zugriff**
setzen. Das ist reine Kosmetik — die Zugriffsrechte aus §3 würden dort ohnehin
nur `<Kein Zugriff>`-Zeilen zeigen —, aber es erspart einen befremdlichen
Anblick.

### Skriptrechte für `Forschende`

S-03 *Projekt anlegen* auf **kein Zugriff**. Projekte legt die Leitung an; dabei
entscheidet sie zugleich über die Beteiligten und damit über die Sichtbarkeit.

---

## 3 · Die Zugriffstrennung einrichten

*Berechtigungen ▸ `Forschende` ▸ Datensätze ▸ **Angepasste Zugriffsrechte***

### Schritt 1 — Tabelle `Projekte`

| Recht | Einstellung |
|---|---|
| Anzeigen | **Eingeschränkt…** → Formel F-24.1 |
| Bearbeiten | **Eingeschränkt…** → dieselbe Formel |
| Erstellen | nein |
| Löschen | nein |
| Feldzugriff | alle |

Formel F-24.1:
```
nicht IstLeer ( FilterWerte ( Projekte::s_BeteiligtIDs ; Projekte::g_MeinePersonID ) )
```

### Schritt 2 — die zwölf abhängigen Tabellen

Für `Abschnitte`, `Schritte`, `Punkte`, `Ergebnisse`, `Aufgaben`, `Dokumente`,
`Dokumentversionen`, `Risiken`, `Manuskripte`, `Einreichungen`, `Hinweise`,
`Verlauf`, `Projektbeteiligte` jeweils:

| Recht | Einstellung |
|---|---|
| Anzeigen | **Eingeschränkt…** → Formel F-24.2 |
| Bearbeiten | **Eingeschränkt…** → dieselbe Formel |
| Erstellen | ja (außer `Projektbeteiligte`: nein) |
| Löschen | **Eingeschränkt…** → dieselbe Formel |

Formel F-24.2, je Tabelle mit dem eigenen Tabellennamen:
```
nicht IstLeer ( FilterWerte ( Abschnitte::s_ZugriffIDs ; Projekte::g_MeinePersonID ) )
```

### Schritt 3 — die frei lesbaren Tabellen

`Personen`, `Rollen`, `VorlageAbschnitte`, `VorlageSchritte`, `VorlagePunkte`,
`zz_Einstellungen`: **Anzeigen ja, Bearbeiten nein** für `Forschende`.

Diese Tabellen enthalten nichts Projektbezogenes. Die Wissensschicht muss lesbar
sein — sie ist der eigentliche Nutzen des Systems.

### Wie es funktioniert

`FilterWerte` vergleicht zwei zeilenweise Listen und gibt die Schnittmenge zurück.

- `s_BeteiligtIDs` bzw. `s_ZugriffIDs` enthält die IDs aller beteiligten Personen
- `g_MeinePersonID` enthält die ID der angemeldeten Person
- Schnittmenge nicht leer → Zugriff. Leer → der Datensatz existiert für diese
  Person nicht.

Beide Felder werden von **Skript S-11** gepflegt (Doc 05).

> **Warum ein denormalisiertes Textfeld und keine Beziehung?** Zugriffsformeln
> wertet FileMaker für *jeden einzelnen Datensatz* aus — bei jeder Suche, bei
> jeder Portalanzeige, bei jedem Export. Ein Bezug oder ein `AuswerteSQL` an
> dieser Stelle ist bei einigen tausend Datensätzen spürbar langsam und im
> Verhalten schwer vorhersagbar. Zwei lokale Felder sind beides nicht.

---

## 4 · Was Sie über diese Lösung wissen sollten

Drei Eigenschaften, die im Betrieb auffallen können:

**Zuordnung wirkt erst nach S-11.** Wird jemand einem Projekt hinzugefügt, sieht
die Person die Unterobjekte erst, wenn die Zugriffslisten neu verteilt sind. Der
Beteiligten-Dialog ruft S-11 direkt auf, der Nachtlauf wiederholt es. Wer die
Zuordnung von Hand in der Tabelle ändert, muss S-11 selbst anstoßen.

**`<Kein Zugriff>` statt Leere.** Landet ein Forschender doch einmal in einer
Ergebnismenge mit fremden Datensätzen, zeigt FileMaker Platzhalterzeilen statt
sie wegzulassen. Deshalb sucht Skript S-02 gezielt statt `Alle Datensätze
anzeigen` zu verwenden. Inhalte sind dabei nie sichtbar — nur die Existenz einer
Zeile.

**Der Nachtlauf umgeht die Trennung — mit Absicht.** Serverzeitpläne laufen unter
einem Konto der Stufe `Administration` und sehen alles. Anders ließen sich die
Kennzahlen nicht projektübergreifend berechnen. Das globale Feld
`g_MeinePersonID` ist dort leer, was ohne Belang ist, weil die Zugriffsformeln
für dieses Rechteset nicht ausgewertet werden.

---

## 5 · Konten anlegen

*Datei ▸ Verwalten ▸ Sicherheit*

Ein Konto je Person, **FileMaker-Datei** als Authentifizierung (oder externe
Authentifizierung über Ihr Verzeichnis, falls vorhanden).

Danach — und das wird gern vergessen — **muss jede Person auch in der Tabelle
`Personen` stehen**, mit exakt demselben Kontonamen im Feld `Kontoname`. Über
diese Verbindung findet S-01 die `__pkPersonID`, und ohne sie greift die
Zugriffstrennung nicht.

| Personen-Feld | Wert |
|---|---|
| `Kontoname` | exakt der FileMaker-Kontoname, Groß-/Kleinschreibung beachten |
| `Stufe` | `Forschende` oder `Leitung` — muss zum Rechteset passen |
| `Mail` | für den Wochenimpuls |
| `Aktiv` | 1 |

**Prüfung nach dem Anlegen:** Mit dem neuen Konto anmelden. Erscheint die Meldung
„Zu Ihrem Konto ist keine Person hinterlegt", stimmt der Kontoname nicht überein.

---

## 6 · Mehrbenutzerbetrieb

Acht bis zwölf gleichzeitige Anwender sind für FileMaker Server eine kleine Last.
Die relevanten Punkte sind nicht Leistung, sondern Verhalten.

### Datensatzsperren

FileMaker sperrt einen Datensatz, sobald jemand darin zu tippen beginnt, und gibt
ihn beim Schreiben wieder frei. Ein zweiter Zugriff meldet dann *„Datensatz wird
von … benutzt"*.

Was das im Alltag heißt:

- **Zwei Personen haken verschiedene Checklistenpunkte desselben Abschnitts ab** —
  problemlos, das sind verschiedene Datensätze.
- **Zwei Personen bearbeiten denselben Projektkopf** — die zweite bekommt die
  Meldung. Selten, aber möglich.
- **Jemand lässt ein Feld über Nacht im Bearbeitungsmodus offen** — der
  Nachtlauf kann diesen Datensatz nicht schreiben. Skript S-08 fängt das ab
  (Fehler 301), überspringt das Projekt und holt es in der nächsten Nacht nach.

**Gegenmaßnahmen im Aufbau:**

1. Nach jeder Änderung `Schreibe Änderung Datens.` — steht in allen Skripten.
2. In den Layouts *Layout-Einstellungen ▸ Datensatz beim Verlassen speichern*
   ohne Rückfrage — dann bleibt nichts unabsichtlich offen.
3. Auf dem Server **Leerlaufzeit setzen:** *Konfiguration ▸ FileMaker-Clients ▸
   Nicht aktive Clients trennen nach* — 3 Stunden ist ein guter Wert. Verhindert
   über Nacht offene Sperren zuverlässiger als jede Ermahnung.

### Globale Felder gelten je Sitzung

`g_MeineStufe`, `g_MeinePersonID`, `g_MeinKonto` und `g_Eingabe1` sind global.
In FileMaker heißt das: **jede angemeldete Person hat eigene Werte**, Änderungen
wirken nicht auf andere.

Genau darauf beruht die Zugriffstrennung — jede Sitzung prüft gegen ihre eigene
Personen-ID.

**Umkehrschluss, der leicht übersehen wird:** Gemeinsame Einstellungen dürfen
niemals in globalen Feldern liegen. Fristen, Absenderadresse und Zeitachsenbreite
stehen deshalb als normale Felder in `zz_Einstellungen`.

### Serverzeitpläne und laufende Sitzungen

Der Nachtlauf um 03:00 kollidiert normalerweise mit niemandem. Sollte doch jemand
arbeiten, greift die Fehlerbehandlung aus S-08. Die übersprungenen Projekte landen
im Verlauf, sodass am Morgen sichtbar ist, was nicht gerechnet wurde.

### Was Sie nicht brauchen

Für diese Größenordnung ist **nichts** davon nötig: Trennung in Daten- und
Oberflächendatei, Caching-Strategien, eigene Sperrverwaltung, WebDirect-Tuning.
Eine Datei auf einem Server, zwölf Clients — das ist der Normalfall, für den
FileMaker gebaut ist.

---

## 7 · Prüfung der Rechte

Sechs Prüfungen. Alle **nach** dem ersten S-11-Lauf durchführen.

| # | Prüfung | Erwartung |
|---|---|---|
| 1 | Als Forschende anmelden, die einem Projekt zugeordnet ist | genau dieses Projekt sichtbar |
| 2 | Als Forschende ein fremdes Projekt suchen (Titel bekannt) | kein Treffer |
| 3 | Als Forschende Layout `02` aufrufen | nicht im Layoutmenü |
| 4 | Als Leitung anmelden | alle Projekte in der Liste |
| 5 | Person einem Projekt zuordnen, S-11 laufen lassen, als diese Person anmelden | Projekt samt Abschnitten und Checkliste sichtbar |
| 6 | Als Forschende einen Datensatz exportieren | nur eigene Projekte im Export |

**Prüfung 6 ist die wichtigste.** Sie zeigt, ob die Trennung wirklich im
Rechtesystem sitzt und nicht nur in der Oberfläche. Fällt sie durch, ist irgendwo
statt *Eingeschränkt* ein *Ja* gesetzt.
