# Das große Streiche-Spiel

Ein Schleich-Spiel, entworfen von einer 8-Jährigen und gebaut mit Claude Code.

Ein Kind schleicht durch den Tag einer fremden Familie und spielt einer Person
Streiche — ohne dabei gesehen zu werden.

---

## Spielen

**Doppelklick auf `streiche.html`.** Fertig.

Keine Installation, kein Server, kein Internet nötig. Alles steckt in dieser einen
Datei — auch die Musik (die wird per Web Audio live erzeugt, es gibt keine
Sound-Dateien).

### Steuerung

| Taste | Was passiert |
|---|---|
| Pfeiltasten | Laufen |
| Leertaste | Streich machen (nur direkt am Ziel) / Weiter |
| ← → | In Menüs auswählen |
| ↑ ↓ | Im Schwierigkeits-Menü die Zeit einstellen |
| M | Musik an/aus |

### Spielregeln

- Lauf zum blinkenden Ziel und drück **Leertaste** → dann suchst du aus zwei
  Streichen einen aus.
- Der gelbe Kegel ist das Blickfeld. Wird er **rot**, wirst du gesehen.
- In einem Möbelstück stehen = **versteckt**, da passiert nichts.
- **❗ über einem Kopf** = die Person dreht sich gleich um.
- Leben aufgebraucht = verloren. Zeit abgelaufen = dieser Streich fällt aus,
  aber es geht weiter.

---

## Was das Spiel kann

**Vor dem Start stellst du ein:**

1. **Namen** — Spielfigur, Erwachsener, seine Frau, zwei Kinder
2. **Tag** — 🏠 Wochenende, 💼 Arbeitstag oder 👵 Oma passt auf
3. **Opfer** — 👨 Papa · 👩 Mama · 👧 Kind 1 · 👦 Kind 2 (am Oma-Tag ist immer die Oma dran)
4. **Schwierigkeit** — 🐣 Leicht (3–6 J.) · 🙂 Mittel (6–10 J.) · 🔥 Schwer (ab 10 J.)
   plus Zeit pro Streich, 20–60 Sekunden in 5er-Schritten

**Im Spiel:**

- 10 bis 13 Level, von früh morgens bis in die Nacht
- **👵 Oma-Tag:** Die Eltern sind den ganzen Tag weg, die Oma macht alles —
  Handtasche, Frühstück, Schule bringen, Mittagessen, Abholen mit dem Fahrrad,
  Mittagsschlaf, Toniebox, Mensch ärgere dich nicht, Abendessen, Serie gucken.
  Alle Oma-Streiche hat die Erfinderin ausgesucht.
- Der Raum wird im Lauf des Tages immer dunkler
- Musik passt zur Tageszeit: Einschlaf-Musik morgens und nachts, rockig
  tagsüber, ruhig am Abend
- **Level-Aufbau:** Level 1 passt nur das Opfer auf, dann kommt Stück für
  Stück die Familie dazu. Im letzten Level sind alle da — und schneller.
- **Streiche mit Spätzündung:** ein Streich (z. B. roter Senf in die
  Ketchup-Flasche) fällt erst zwei Level später beim Abendessen auf.
- Jeder Raum hat eigene Streiche pro Opfer — bei einem Kind geht der Tag
  sogar in die **Schule** und in die **große Pause**, wo die Lehrerin und die
  Mitschüler aufpassen.

---

## Für die Weiterarbeit (Technik)

Alles steckt in `streiche.html`. Kein Build, keine Abhängigkeiten.

`index.html` ist **Gretas Spiele** – ihre eigene Spieleseite (lila/türkis, Einhorn und Reh).
Neue Spiele bekommen dort eine eigene Karte. Der Ordner lässt sich so wie er ist als
statische Seite hochladen (z. B. GitHub Pages) – noch nicht veröffentlicht. Einfach im
Editor öffnen, ändern, Browser neu laden.

### Aufbau der Datei

| Abschnitt | Was drin steht |
|---|---|
| `SCHWIERIGKEIT` | Leicht/Mittel/Schwer: Leben, Zeit, Sichtweite, Tempo, max. Aufpasser |
| `WACHTYP` | Wer wie gut guckt: `gross`, `frau`, `k1`, `k2`, `lehrer`, `freund` |
| `RAEUME` | Alle Räume: Möbel, Laufwege, Ziel, Streiche |
| `RAEUME.klassenzimmer` / `.pause` / `.kindMorgen` / `.kindAbend` / `.kindBett` | Nur im Kind-Modus |
| `RAEUME.<raum>.oma`, `.toniebox`, `.brettspiel` | Oma-Tag: Oma-Streiche in den normalen Räumen + zwei eigene Räume |
| `RAEUME.cafe` / `.grillen`, `ORTE`, Bildschirm `s-ort` | Schon fertig, aber noch nicht im Spiel — für die Verabredung aufgehoben |
| `TAGE` | Die drei Tagespläne (welcher Raum in welchem Level) |
| `MUSIK` | Die vier Musikstücke als Notenmuster |
| Rest | Menüs, Bewegung, Zeichnen |

### Wie ein Raum aufgebaut ist

```js
raumName: {
  raum: "Küche", emoji: "☕",
  aufgabe: "...",                 // Text oben; Standard = Streiche für den Papa
  boden: "#...", wand: "#...",
  moebel: [ {x,y,w,h,farbe,emoji,label} ],   // gleichzeitig die Verstecke
  ziel:   { x, y, emoji, label },
  routen: { gross:[...], frau:[...], k1:[...], k2:[...] },  // Laufwege
  spielerStart: { x, y },
  maxWachen: 2,                   // optional, z.B. Büro (Kinder sind in der Schule)
  wachOrdnung: [...],             // optional, z.B. Schule (Lehrerin statt Eltern)
  optionen: [ {emoji, text, folgeEmoji, folge, merke?} ],
  vorspann: { wenn:"senf", emoji, text },    // optional: Spätzünder-Auflösung

  frau: { aufgabe, ziel, optionen, vorspann? },   // Streiche, wenn die Mama dran ist
  kind: { aufgabe, ziel, optionen, vorspann? },   // Streiche, wenn ein Kind dran ist
  oma:  { aufgabe, ziel, optionen, emoji?, wachOrdnung?, routeOma? }  // Oma-Tag
}
```

`opferAuftrag()` sucht das passende Set aus: `kind` für k1/k2, sonst das Set
mit dem Namen des Opfers (`frau`, `oma`), sonst die Felder direkt im Raum (= Papa).
Am Oma-Tag läuft die Oma den Weg vom Papa (`routen.gross`), außer der Raum
hat `routen.oma` oder das Set hat `routeOma`.

### Platzhalter in Texten

`{K}` Spielfigur · `{G}` der Papa · `{F}` die Mama · `{K1}` `{K2}` die Kinder ·
`{O}` das aktuelle Opfer · `{A}` das andere Kind · `{L}` die Lehrerin ·
`{OMA}` die Oma · `{F1}` `{F2}` die Freunde der Eltern

### Spielfeld

900 × 560 px. Ist der Text in der Kopfzeile zu lang, wird er automatisch
kleiner. Bespielbar ist x = 51…849 und y = 94…509 (oben sitzt die
Kopfzeile). Möbel dürfen sich nicht mit dem Ziel oder dem Spieler-Startpunkt
überlappen.

### Testen ohne Spielen

In der Browser-Konsole (F12) lässt sich ein ganzer Tag durchklicken:

```js
neustart(); tagIndex = 1; tagWaehlen(); familieFertig();
opferIndex = 2; opferFertig(); waehleSchwer(1); spielStarten();
for (let n = 0; n < 70; n++) {
  if (zustand === 'vorspann') weiter();
  if (zustand === 'brief') { console.log(index + 1, szene.raum, wachen.map(w => w.name)); document.getElementById('btn-brief').click(); }
  if (zustand === 'spiel') { spieler.x = auftrag.ziel.x; spieler.y = auftrag.ziel.y; versuchStreich(); }
  if (zustand === 'wahl') waehle(0);
  if (zustand === 'folge') weiter();
  if (zustand === 'ende') break;
}
```

---

## Was noch geplant ist

Wunschliste der Erfinderin, in ihrer Reihenfolge:

- [x] Nur der Papa
- [x] Nur die Mama
- [x] Nur ein Kind
- [x] Nur das andere Kind
- [x] **Oma-Tag** — die Eltern sind weg, die Oma passt den ganzen Tag auf
- [ ] **Verabredung** — bei Wochenende/Arbeitstag aussuchen: ohne oder mit
  Verabredung; wer sich verabredet (Kinder / Eltern / alle — mehr Leute = schwerer);
  Freunde kommen zu Besuch oder man geht hin (☕ Café / 🍖 Garten mit Grillen).
  Café- und Grill-Streiche sind schon gebaut. **Genaue Regeln nochmal mit ihr klären.**
- [ ] **Sprachen** — Deutsch, Englisch und ein paar andere bekannte Sprachen
  zum Einstellen (damit man es z. B. auch in Amerika spielen kann)
- [ ] **Beide Kinder zusammen** — mal ist das eine dran, mal das andere
- [ ] **Beide Eltern zusammen**
- [ ] **Die ganze Familie** — jedes Level ein anderes Opfer

Technisch ist die Basis dafür schon da: `OPFER_IDS` erweitern und in
`opferAuftrag()` pro Level ein Opfer auswürfeln oder abwechseln lassen.

### Aus dem Spieletest (Oma, Mama, Titus)

- Titus (jünger) ist mehrfach rausgeflogen → daraufhin kamen die
  Schwierigkeitsgrade rein. **Für Loris (3) „Leicht" nehmen.**
- Noch offen zu prüfen: ob „Leicht" jetzt wirklich schaffbar ist.

---

## Idee & Bau

Konzept, Streiche und alle Regeln: die 8-jährige Erfinderin.
Umsetzung: Claude Code.
