# Arbeitsmedium-Mechanik: Tutor Board

**Voraussetzung:** Das Board braucht ein Tutor-Board-Konto (Einladung nötig,
beta.probable.work). Die MCP-Verbindung wird außerhalb von lernclaude
konfiguriert.

**Bedienung des Boards:** den Tool-Beschreibungen und den Server-Instructions des
Tutor-Board-MCP folgen. Diese Datei sagt nur, was der Lern-Loop auf dem Board haben
will. Bei Widerspruch gewinnt der Server; eine Eigenheit des Servers wird dort
beschrieben oder behoben, nicht hier umschifft.

## Ankommen (in dieser Reihenfolge, ohne nachzufragen)

1. Ein Kurs = ein Board, benannt nach dem Kurs. Gibt es keins, eines anlegen.
   **Nie auf dem Board eines anderen Kurses bauen.** Mehrere Lern-Sessions laufen
   oft parallel auf demselben Konto: vor jedem schreibenden Call prüfen, dass das
   gewählte Board noch das eigene ist.
2. Den User aufs Board holen: erst über das Board selbst einladen, und nur wenn
   niemand zuschaut, Firefox auf die konkrete Board-URL öffnen, nicht auf die
   Startseite.
3. Ankunftsbericht lesen: gibt es ungelesene Abgaben, **zuerst reviewen**, bevor
   irgendetwas Neues entsteht. Ein bereits ausgegebenes, aber ungerechnetes
   Häppchen wird **wieder vorgelegt, nicht ersetzt**.

## Struktur (verbindlich)

- **Tab 0 = „Übersicht"** — die Kursübersicht (§Kursübersicht der Kurs-CLAUDE.md)
  samt Fortschrittsspiegel, siehe den Abschnitt unten. Re-Entry-Punkt des Boards
  und Spiegel von `todo.md`/`fehlermuster.md`, nicht deren Ersatz. Fehlt der Tab,
  **jetzt** anlegen und an Position 0 holen.
- **Jeder weitere Tab = genau EIN Häppchen.** Titel kurz und referenzierbar
  (`H03 …`); der ganze Satz gehört in die Tab-Beschreibung.
- **Quiz-Häppchen** als eigener Tab (`Q01 <Thema>`), eine Frage pro Block,
  geantwortet wird im selben Tab.

## Blöcke bauen

- **Formeln in `markdown`-Blöcke**, dort rendert LaTeX.
- **Antwortformat vor Freitext-Mathe bevorzugen**: Zahlenfelder mit vorgedruckter
  Form, oder `radio`. Exponenten zu tippen frisst mehr Zeit als das Rechnen, und
  ein Radio hat kein leeres Feld, in dem man sich verstecken kann.
- **Wunde Stellen in Einzelfelder zerlegen** (Vorzeichen, Vorfaktor, Exponent
  getrennt) und in jedem Schritt den Wert neu hinschreiben, statt auf die Zeile
  darüber zu verweisen — sonst wird die Zahl von oben durchgereicht.
- **Jede Aufgabe endet mit einem `submit`-Knopf mit Antwortschlüssel**, sobald die
  Antworten objektiv prüfbar sind. Der Schlüssel ist Teil der Aufgabe: vor dem
  `select_tab` jeden Eintrag selbst nachrechnen und die Zuordnung Feld → Wert
  prüfen. Ein falscher Schlüssel markiert eine richtige Abgabe als falsch und
  verfälscht das Fehlermuster. Gleichwertige Schreibweisen als Liste angeben.

## Kursübersicht (Tab 0)

Tab 0 ist der eine Übersichts-Tab des Kurses — der Vertrag aus §Kursübersicht der
Kurs-CLAUDE.md und der Fortschrittsspiegel in einem. Reihenfolge der Blöcke:

1. **Prüfung** — Eckdaten, Format, Bestehen.
2. **Themenkarte mit Fortschritt** — als Tabelle, je Thema eine Anzeige
   (🔴/🟠/🟡/🟢 oder `▓▓▓░░`, mit Legende) und die zugehörigen Häppchen namentlich
   („→ H03 …"); darunter die offenen Fehlermuster in je einem Satz und die Zeile
   `Fortschritt: x/y Häppchen`. Dieser Block wird nach jedem Review per
   `update_block` nachgezogen.
3. **Themen erklärt** — je Thema Idee, Prüfungsanforderung, Notation, Falle,
   Material, höchstens 6 Sätze. Formeln in `markdown`-Blöcke.
4. **Materialien** — jede Datei mit Pfad und einem Halbsatz beschrieben. Fotos
   als Bild-Block, einzelne PDF-Seiten nur situativ im Häppchen, das sie braucht
   (als PNG gerendert), nie ganze Skripte.
5. **Vereinbarung** — drin / nicht drin / offene Fragen. Darunter genau EIN
   `submit`-Knopf („Gelesen & einverstanden"); sonst **keine Eingabefelder** im Tab.

Bestätigung: auf den Klick warten (§„Auf den User warten"); sagt der User es
im Chat, gilt das genauso. Danach in `todo.md`
`Übersicht: bestätigt YYYY-MM-DD` eintragen und erst dann das erste Häppchen.

**Tab 0 wird immer an Ort und Stelle geändert**, auch beim Umbau: Blöcke mit
`update_block` ersetzen, mit `append_blocks` ergänzen, mit `remove_block`
entfernen. Kein `show_board`/`clear_board` für eine Änderung an einem Tab — das
setzt das ganze Board zurück. `clear_board` nur für ein leeres oder für ein
falsch aufgebautes Board.

Nachziehen bei einem Board, dessen Tab 0 bisher nur der Fortschrittsspiegel war:
die fehlenden Blöcke ergänzen und in die Reihenfolge oben bringen.

## Häppchen übergeben und einsammeln

- **Ein Häppchen zur Zeit.** Das nächste wird erst gebaut, wenn das aktuelle
  abgegeben und reviewt ist; Wartezeit wird nicht mit Vorbauen gefüllt. Liegt ein
  Tab ungerechnet, wird er wieder vorgelegt (§Ankommen), nicht durch einen neuen
  ersetzt. (EP2 2026: drei offene Tabs nebeneinander, eines davon drei Tage
  ungerechnet; User: „räum die Tabs mal auf".)
- Bauen mit `create_tab(select: false)`, in der Übersicht eintragen, **dann erst**
  `select_tab` — den User nie mitten im Rechnen wegreißen.
- Danach auf die Abgabe warten, siehe §„Auf den User warten".

## Auf den User warten

Gilt für jeden Wait: Übersichts-Bestätigung, Häppchen-Abgabe, „Noch eins?".

- Warten, wie der MCP es beschreibt (bevorzugt der Weg, der keine Modellaufrufe
  kostet). Nach dem Aufwachen sagt das Ereignis, ob der User geklickt oder
  geschrieben hat; entsprechend Board oder Chat lesen.
- **Den Wait als Monitor führen, nicht als Hintergrund-Shell.** Claude Code
  räumt Hintergrund-Shells ab, sobald der Kernel Speicherdruck meldet
  („stopped because the system is running low on memory"), und das trifft auf
  diesem Host auch bei mehreren freien GB zu. Monitore sind davon ausgenommen.
  `persistent` setzen, damit auch das Zeitlimit des Monitors nicht dazwischen
  kommt. Vom Stream nur die Ereigniszeile durchlassen und die Zeilen wegfiltern,
  die bloß die Verbindung offen halten — kommt jede davon als Meldung an, stoppt
  der Monitor wegen zu vieler Meldungen.
- **Wird der Wait trotzdem beendet, ohne gefeuert zu haben**: einmal das Board
  lesen — oft liegt die Abgabe längst vor; sonst den Wait höchstens **zweimal**
  neu starten; danach dem User in einem Satz sagen, dass er sich nach dem Abgeben
  kurz melden soll. Keine dritte Runde.
- Stirbt der Wait wiederholt, den Wait im Modellkontext nehmen, den der MCP als
  Rückfallebene anbietet — nicht als Standard.

## Review

- Abgaben samt Score vom Board lesen. Trägt der Tab eine Zeichnung, sie holen —
  **nie über eine ungesehene Zeichnung raten**.
- Die Korrektur als Block **am selben Tab** zeigen, nicht nur im Chat: Zitat →
  warum falsch → was stattdessen, **je Fehler höchstens fünf Sätze**. Ist der
  Score falsch, weil der Schlüssel falsch war, das auf dem Board richtigstellen
  und dem User sagen.
- Danach die Übersicht per `update_block` nachziehen (Themen-Level,
  Häppchen-Status, `Fortschritt:`-Zeile). Fertig reviewte Tabs **bleiben auf der
  Leiste**, samt Korrektur: der User will sie weiter sehen. Archivieren nur auf
  ausdrückliche Bitte; der Eintrag in der Übersicht bleibt in jedem Fall.

## Erklär-Clip zu jedem Häppchen (Nutzervorgabe 17.09.2026)

Nach jedem neuen Häppchen-Tab prüfen, ob ein kurzer Clip die Idee trägt — eine
Funktion, die sich bewegt, eine Ableitung als Tangente, ein Vektor, der sich dreht, eine
Konvergenz, eine Skizze, die entsteht. Trägt er sie, wird er gebaut, **während der User
den Tab schon bearbeitet**, und als Video-Block unten an denselben Tab gehängt. Reine
Wiedererkennung (Listen, Begriffspaare, Ankreuz-Drills) bekommt keinen Clip.

- **Reihenfolge:** Tab bauen, in der Übersicht eintragen, `select_tab` — **dann** den
  Clip anstoßen. Der User wartet nie auf einen Render.
- **Per Opus-Subagent, nicht im eigenen Kontext** (Token-Sparsamkeit): `Agent` mit
  `model: opus`, Auftrag = Skill `manim-kit` laden, eine Szene, 15–25 s, eine Idee,
  **1080p bei 60 fps** (`-q h`, manim-kit-Standard; Nutzervorgabe 18.09.2026, nie Draft-Qualität), unter 8 MB je Clip (längere Videos als Einzelclips, nicht herunterkomprimieren), die Standard-Tonspur/Musik des manim-kit **nicht** abbestellen;
  Rückgabe **nur** der mp4-Pfad (oder
  `RENDER FAILED: <Grund>`).
- **Immer mit Voiceover** (Nutzervorgabe 18.09.2026): der Clip wird als `VoiceoverScene`
  gebaut (`manim-kit new NAME --template narrated`), jede Animation in einem
  `with self.voiceover(...)`-Block, der die Idee in ein bis zwei Sätzen sagt — nicht die
  Beschriftung vorlesen, sondern erklären, was gerade passiert. Stimme:
  **`kokoro-v1:af_sarah`** (offline, kostenlos, englisch). Eine offline-deutsche Stimme
  gibt es in dieser Version nicht; die bezahlte `gemini-flash-tts:Aoede:de` hat der User
  am 18.09. abgelehnt („Englisch, kostenlos"). Vor dem ersten Clip je Host einmal
  `manim-kit voice setup` (354 MB Modell). Die Musik mischt manim-kit bei Narration
  automatisch unter die Stimme — nichts extra einstellen. Der Hauptkontext lädt das mp4 hoch
  und hängt es als Video-Block unten an denselben Tab.
- **Fail into silence:** scheitert der Render, bleibt es beim Textblock; kein Hinweis an
  den User, keine zweite Runde. `manim-kit doctor` einmal pro Host.
- **Nie in die Übersicht.** Ein Clip an einer Korrektur (wiederkehrendes, geometrisches
  Fehlermuster) bleibt zusätzlich erlaubt, gleiche Mechanik.

## „Noch eins?“ (Quickie)

Die Frage nach dem nächsten Quickie gehört **aufs Board, nicht ins Terminal** —
der User schaut ohnehin dorthin, und ein Klick ist billiger als eine getippte
Antwort.

- Ans Ende des Häppchen-Tabs, direkt unter die Korrektur, genau EINEN
  `submit`-Knopf („Noch eins?“). Kein Gegenstück zum Ablehnen — wer aufhören
  will, klickt einfach nicht oder sagt es im Chat; ein „für heute reicht's“-Knopf
  macht das Aufhören zur angebotenen Option und arbeitet gegen die Gewohnheit.
- Danach auf den Klick warten (§„Auf den User warten"). Kommt der Klick, folgt
  das nächste Häppchen als neuer Tab; bleibt er aus oder sagt der User ab, ein
  Satz Abschied.
- Antwortet er stattdessen im Chat, gilt das genauso — den Knopf dann nicht
  wiederholen.
