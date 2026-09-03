# 🔓 Lösung — XSS-02 Suche mit Filter

> ⚠️ **Spoiler.** Erst lesen, wenn du selbst am Filter gescheitert bist.

---

## Der Filter

```js
function sanitize(input) {
  var blocked = ['<script', 'onerror', 'onload', 'javascript:'];
  var out = input;
  for (var i = 0; i < blocked.length; i++) {
    out = out.split(blocked[i]).join('');   // alle Vorkommen raus, EIN Durchlauf
  }
  return out;
}
```

Sieht wehrhaft aus, hat aber **drei** klassische Fehler. Jeder einzelne reicht zum Bypass.

### Warum die XSS-01-Payload stirbt

`<img src=x onerror=showFlag()>` → der Filter entfernt `onerror` →
`<img src=x =showFlag()>`. Das `<img>` hat keinen gültigen Handler mehr, `src=x`
schlägt zwar fehl, aber es gibt kein `onerror` → **nichts feuert.** Genau so soll es
den Anfänger ausbremsen.

---

## Bypass A — Groß-/Kleinschreibung (der einfachste)

Der Filter vergleicht mit `split('onerror')` — **case-sensitiv**. HTML-Attributnamen
sind aber **case-insensitiv**: der Browser liest `oNerror` als `onerror`.

```html
<img src=x oNerror=showFlag()>
```

Der Filter findet kein exaktes `onerror` (wegen des großen `N`) → lässt alles stehen →
der Browser parst `oNerror` als gültigen `onerror`-Handler → `src=x` scheitert →
`showFlag()` feuert. ✅

## Bypass B — Verschachtelung (Non-Recursion)

Der Filter läuft nur **einmal** und prüft das Ergebnis nicht erneut. Wenn das verbotene
Wort **nach** dem Entfernen neu entsteht, rutscht es durch:

```html
<img src=x ononerrorerror=showFlag()>
```

Ablauf: `split('onerror')` zerlegt `ononerrorerror` in `["on", "error"]`, `join('')`
klebt zu `onerror` zusammen. Ergebnis: `<img src=x onerror=showFlag()>` — ein perfekt
gültiger Handler, den der Filter selbst zusammengebaut hat. ✅

## Bypass C — Unvollständige Blacklist (zur Vollständigkeit)

Die Liste kennt nur `onerror`/`onload`. Es gibt Dutzende Handler (`onclick`,
`onmouseover`, `ontoggle`, `onfocus` …). Bei per `innerHTML` eingefügten Elementen
feuern die meisten aber **nicht automatisch** (kein Page-Load, kein Nutzer-Event) —
`onerror` an `<img>` ist der zuverlässige Auto-Trigger. Darum sind A und B die
praktikablen Wege. Merke dir C trotzdem: In anderen Kontexten (echtes Page-Rendering
statt innerHTML) ist ein nicht-gelisteter Auto-Handler wie `<svg onload>` oder
`<body onload>` oft der Weg.

---

## Als URL

```
index.html?q=<img src=x oNerror=showFlag()>
```

URL-kodiert:
```
index.html?q=%3Cimg%20src%3Dx%20oNerror%3DshowFlag()%3E
```

---

## 🚩 Die Flag

```
TELQRR{bl4ckl1st_f1lt3r_1st_k3in_schutz}
```

Der Name ist Programm: Eine Blacklist ist **kein** Schutz.

---

## 🛡️ Wie man es richtig macht

Der Filter ist konzeptionell falsch — man **repariert** ihn nicht, man **ersetzt den
Ansatz**:

```js
// FALSCH: eigene String-Ersetzung / Blacklist
el.innerHTML = 'Ergebnisse für: <b>' + sanitize(q) + '</b>';

// RICHTIG (reiner Text): gar kein HTML-Parsing
el.textContent = 'Ergebnisse für: ' + q;

// RICHTIG (wenn HTML wirklich nötig): erprobter Whitelist-Sanitizer
el.innerHTML = DOMPurify.sanitize('<b>' + q + '</b>');
```

Plus **CSP** als zweite Linie, die Inline-Handler ganz verbietet.

---

## 📖 Kernlektion

Blacklists scheitern an drei Fronten gleichzeitig — **Case**, **Rekursion/Reihenfolge**
und **Vollständigkeit**. Man müsste jeden Angriff im Voraus kennen, um alle zu blocken.
Sicheres XSS-Handling dreht das um: **erlauben, was bekannt sicher ist** (Whitelist /
Encoding), statt zu verbieten, was gerade als böse bekannt ist.
