# XSS-01 — Reflektierter Gruß

| | |
|---|---|
| **Schwachstelle** | Cross-Site Scripting (XSS), reflektiert / DOM-basiert |
| **Stufe** | 1 — Einsteiger |
| **Ziel** | Eigenes JavaScript im Seitenkontext ausführen und die Flag freischalten |
| **Flag-Format** | `TELQRR{...}` |
| **Setup** | Keins. `index.html` einfach im Browser öffnen (Doppelklick). |

---

## Die Story

Das "Gästebuch 3000" begrüßt dich mit deinem Namen. Der Entwickler hat den Namen
leider **ungefiltert** in die Seite geschrieben. Das ist die klassischste
Web-Lücke überhaupt.

Irgendwo auf der Seite liegt eine Funktion, die die Flag anzeigt — sie wird aber
**nie von selbst aufgerufen**. Dein Job: bring die Seite dazu, deinen Code
auszuführen, und ruf sie damit auf.

## Dein Auftrag

1. Finde die Stelle, an der deine Eingabe ungefiltert in die Seite gelangt.
2. Führe damit **beliebiges JavaScript** aus (der klassische Beweis: ein `alert()`).
3. Schalte die versteckte Flag frei und lies sie ab.

Es gibt **zwei Wege**, deine Eingabe reinzugeben:
- das Namensfeld auf der Seite, oder
- den URL-Parameter `?name=DEINE_EINGABE` (echtes "reflected"-Feeling).

---

## Hinweise (nur aufklappen, wenn du stecken bleibst)

<details>
<summary>💡 Hinweis 1 — Wo ist die Lücke?</summary>

Öffne die `index.html` in einem Texteditor und such nach dem Wort `innerHTML`.
Alles, was per `innerHTML` in eine Seite geschrieben wird, wird als **HTML
interpretiert** — nicht als reiner Text. Was passiert also, wenn dein "Name"
selbst HTML ist?
</details>

<details>
<summary>💡 Hinweis 2 — Warum tut sich bei &lt;script&gt; nichts?</summary>

Ein `<script>`-Tag, das per `innerHTML` eingefügt wird, führt der Browser aus
Sicherheitsgründen **nicht** aus. Du brauchst HTML, das JavaScript über einen
**Event-Handler** startet — etwas, das "von selbst" feuert, ohne Klick.
Stichworte: ein Bild, dessen Quelle absichtlich kaputt ist, oder ein SVG.
</details>

<details>
<summary>💡 Hinweis 3 — Welche Funktion muss ich aufrufen?</summary>

Im `<script>`-Block gibt es eine Funktion, deren Name schon verrät, was sie tut.
Sie steht global zur Verfügung. Du musst sie aus deinem injizierten Code heraus
aufrufen.
</details>

---

## Erfolg sieht so aus

Wenn dein Angriff sitzt, erscheint unter dem Ausgabefeld ein grüner Kasten:

```
🚩 Flag: TELQRR{...}
```

Trag die Flag dann unten in diese Datei ein (Abschnitt "Gelöst") oder ins
Master-README.

---

## Was du hier lernst

- **Reflected/DOM-XSS:** Nutzereingabe landet ungefiltert im HTML → Angreifer-Code läuft.
- **Warum `innerHTML` gefährlich ist** und `textContent` sicher wäre.
- **Event-Handler-Payloads** (`onerror`, `onload`) statt `<script>`.
- Dass „nur clientseitig" nicht „harmlos" heißt: der Code läuft im Kontext des Opfers.

## Wie man es richtig macht (Verteidigung)

- Ausgabe **kontext-korrekt encoden**; für reinen Text `textContent` statt `innerHTML`.
- Eingaben serverseitig **validieren**, nicht nur clientseitig.
- **Content-Security-Policy (CSP)** setzen, die Inline-Handler blockt.
- Framework-Auto-Escaping nutzen und nicht mit `dangerouslySetInnerHTML` / `v-html` aushebeln.

> Die komplette Lösung mit fertiger Payload steht in **`loesung.md`** — erst reinschauen,
> wenn du es selbst versucht hast.

---

## Gelöst

- [x] Flag gefunden am: `2026-09-02`
- Notizen / eigener Payload: `<img src=x onerror=showFlag()>` — Standardweg. `<script>` läuft per `innerHTML` nicht, `onerror` einer kaputten Bild-`src` feuert sofort ohne Klick.
- Flag: `TELQRR{r3fl3ct3d_xss_1st_d3r_3inst1eg}`
