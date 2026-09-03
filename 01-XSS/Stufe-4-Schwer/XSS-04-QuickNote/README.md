# XSS-04 — QuickNote

| | |
|---|---|
| **Schwachstelle** | XSS trotz Zeichen-Filter (Reihenfolge-Bug) |
| **Stufe** | 4 — Schwer |
| **Ziel** | `showFlag()` triggern, obwohl `< > " '` gefiltert werden |
| **Flag-Format** | `TELQRR{...}` |
| **Setup** | Keins. `index.html` im Browser öffnen. |

## Die Story

QuickNote entfernt konsequent alle `< > " '` aus deiner Eingabe — damit ist Tag-
Injection eigentlich tot. Trotzdem ist die Seite verwundbar. Der Fehler steckt nicht
im Filter selbst, sondern in **dem, was die App danach noch mit deiner Eingabe macht**.

## Auftrag

1. Öffne `index.html`. Der rohe `<img ...>`-Payload wird zerlegt (keine `<>` mehr).
2. **Lies `render` im Quelltext ganz genau** — vor allem, was **nach** dem Filter passiert.
3. Finde einen Weg, deine gefährlichen Zeichen so einzuschleusen, dass sie den Filter
   passieren und **erst danach** wieder zu echten `< >` werden.

## Hinweise

<details><summary>💡 Hinweis 1</summary>
Der Filter läuft <b>zuerst</b>. Danach ruft die App noch eine zweite Funktion auf deiner
Eingabe auf, bevor sie sie anzeigt. Welche? Was tut sie mit Prozentzeichen?
</details>

<details><summary>💡 Hinweis 2</summary>
<code>decodeURIComponent</code> verwandelt <code>%3C</code> in <code>&lt;</code> und
<code>%3E</code> in <code>&gt;</code> — <b>nachdem</b> der Filter schon durchgelaufen ist.
Zum Filter-Zeitpunkt sind das harmlose Buchstaben und Prozentzeichen.
</details>

<details><summary>💡 Hinweis 3</summary>
Nimm deinen XSS-01-Payload und ersetze die spitzen Klammern durch ihre
Prozent-Kodierung. Ins Textfeld eingeben (nicht über die URL — die würde zu früh
dekodieren).
</details>

## Was du lernst

- **Reihenfolge von Filter und Decode ist eine eigene Schwachstellenklasse.** Wer erst
  filtert und dann dekodiert (oder mehrfach dekodiert), hebt den Filter selbst aus.
- Kodierte Payloads (`%3C`, Doppel-Encoding) sind ein Standard-Umgehungsvektor.

## Richtig machen

- **Erst dekodieren, dann validieren/encoden** — nie umgekehrt. Kanonisiere die Eingabe
  vollständig, bevor irgendein Filter greift. Für die Ausgabe: `textContent` statt `innerHTML`.

> Lösung in `loesung.md` — erst nach eigenem Versuch.

## Gelöst
- [ ] Flag gefunden am: `____-__-__`
- Payload:
