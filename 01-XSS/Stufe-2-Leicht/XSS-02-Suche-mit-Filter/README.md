# XSS-02 — Suche mit Filter

| | |
|---|---|
| **Schwachstelle** | Cross-Site Scripting (XSS) trotz Eingabefilter |
| **Stufe** | 2 — Leicht |
| **Ziel** | Den „XSS-Filter" umgehen und die Flag freischalten |
| **Flag-Format** | `TELQRR{...}` |
| **Setup** | Keins. `index.html` im Browser öffnen. |

---

## Die Story

Nach dem Debakel mit dem Gästebuch (→ XSS-01) hat der Entwickler „nachgerüstet":
Die Suche hat jetzt einen **XSS-Filter**, der die gefährlichen Wörter aus deiner
Eingabe entfernt. Er ist mächtig stolz darauf und behauptet, die Seite sei jetzt
„100 % sicher".

Das Dumme: Sein Filter ist eine **Blacklist** — er verbietet ein paar bekannte böse
Wörter. Blacklists sind fast immer kaputt. Deine erste XSS-01-Payload
(`<img src=x onerror=showFlag()>`) wird tatsächlich zerstört. Aber der Filter hat
Lücken.

## Dein Auftrag

1. Öffne `index.html` und probiere deine alte Payload — sie funktioniert **nicht** mehr.
2. **Lies den Filter im Quelltext** (Rechtsklick → „Seitenquelltext anzeigen", oder die
   Datei im Editor öffnen). Such die Funktion `sanitize`.
3. Finde **mindestens eine** Schwäche im Filter und bau eine Payload, die durchrutscht.
4. Ruf damit `showFlag()` auf und lies die Flag.

Es gibt **mehr als einen** Weg durch diesen Filter. Einer reicht.

---

## Hinweise (nur bei Bedarf aufklappen)

<details>
<summary>💡 Hinweis 1 — Warum stirbt meine alte Payload?</summary>

Der Filter läuft die Liste `blocked` durch und wirft jedes dieser Wörter aus deiner
Eingabe. `onerror` steht drauf — also wird genau dieses Wort entfernt, und dein
`<img>` bleibt ohne funktionierenden Handler zurück. Du musst dafür sorgen, dass am
Ende trotzdem ein gültiger Event-Handler im HTML steht.
</details>

<details>
<summary>💡 Hinweis 2 — Wie genau vergleicht der Filter?</summary>

Schau dir an, **wie** verglichen wird: `split('onerror')`. Das ist
**Groß-/Kleinschreibung-empfindlich**. Frage: Sind HTML-Attributnamen auch
case-sensitive? Zählt für den Browser `onerror` und `oNeRRor` als dasselbe?
(Antwort: für den Browser ja, für den Filter nein.)
</details>

<details>
<summary>💡 Hinweis 3 — Wie oft filtert er?</summary>

Der Filter ersetzt jedes böse Wort in **einem einzigen Durchlauf** und prüft das
Ergebnis danach **nicht erneut**. Was passiert, wenn das verbotene Wort erst
**entsteht**, nachdem der Filter drübergelaufen ist? Denk an ein Wort, das ein
verbotenes Wort in der Mitte enthält, sodass nach dem Herausschneiden ein neues,
sauberes verbotenes Wort übrig bleibt.
</details>

<details>
<summary>💡 Hinweis 4 — Gibt es nicht noch einen dritten Weg?</summary>

Ja. Die Blacklist kennt nur `onerror` und `onload`. Es gibt **Dutzende** Event-Handler.
Nicht jeder feuert allerdings zuverlässig, wenn ein Element über `innerHTML` eingefügt
wird — `onerror` an einem `<img>` mit kaputter `src` ist der verlässlichste. Deshalb
zielen die einfachsten Lösungen darauf, genau dieses `onerror` **doch** durchzubekommen.
</details>

---

## Erfolg sieht so aus

Grüner Kasten unter dem Suchfeld:

```
🚩 Flag: TELQRR{...}
```

---

## Was du hier lernst

- **Blacklist-Filter sind strukturell kaputt.** Man kann sie umgehen über:
  - **Groß-/Kleinschreibung** (`oNerror` statt `onerror`),
  - **Verschachtelung / Non-Recursion** (Filter läuft nur einmal, das böse Wort entsteht neu),
  - **Unvollständigkeit** (der Filter kennt nicht alle gefährlichen Tokens).
- Warum „wir filtern die bösen Wörter raus" fast nie echter Schutz ist.

## Wie man es richtig macht (Verteidigung)

- **Nicht blacklisten, sondern encoden:** kontext-korrektes Output-Encoding, für reinen
  Text `textContent` statt `innerHTML`.
- Wenn HTML erlaubt sein muss: eine **erprobte Sanitizer-Bibliothek** (z. B. DOMPurify)
  mit **Whitelist**, niemals selbstgebaute String-Ersetzung.
- **CSP** als zweite Verteidigungslinie gegen Inline-Handler.

> Vollständige Lösung mit beiden Bypass-Payloads: **`loesung.md`** — erst nach eigenem Versuch.

---

## Gelöst

- [ ] Flag gefunden am: `____-__-__`
- Genutzter Bypass / Payload:
