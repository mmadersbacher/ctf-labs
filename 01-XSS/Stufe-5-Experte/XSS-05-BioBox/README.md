# XSS-05 — BioBox

| | |
|---|---|
| **Schwachstelle** | DOM Clobbering (HTML-Injection ohne JavaScript) |
| **Stufe** | 5 — Experte |
| **Ziel** | `showFlag()` auslösen, obwohl Scripts & Handler gestrippt werden |
| **Flag-Format** | `TELQRR{...}` |
| **Setup** | Keins. `index.html` im Browser öffnen. |

## Die Story

BioBox lässt dich HTML in deinen Steckbrief schreiben, jagt es aber durch einen
Sanitizer: **jedes `<script>` und jeder `on*`-Handler wird entfernt.** Klassisches XSS
ist damit tot — `<img onerror>`, `<svg onload>`, `<script>`: alles wirkungslos.

Die Flag fällt nur, wenn `window.APP_CONFIG.debug` gesetzt ist. Im Code wird
`APP_CONFIG` **nirgends definiert** — also normalerweise `undefined`. Deine Aufgabe:
diese Bedingung wahr machen, **ohne ein einziges Byte JavaScript** auszuführen.

## Auftrag

1. Öffne `index.html`. Überzeuge dich, dass Handler/Script gefiltert werden.
2. Frag dich: Wie kann ich eine `window`-Eigenschaft setzen, **nur mit erlaubten HTML-Tags**?
3. Sorge dafür, dass `window.APP_CONFIG && window.APP_CONFIG.debug` beide truthy sind.

## Hinweise

<details><summary>💡 Hinweis 1 — Stichwort</summary>
Die Technik heißt <b>DOM Clobbering</b>. HTML-Elemente mit <code>id</code>/<code>name</code>
werden vom Browser als benannte Eigenschaften auf <code>window</code> bzw. auf ihrem
Eltern-Element zugänglich gemacht — ganz ohne JavaScript.
</details>

<details><summary>💡 Hinweis 2 — window-Property setzen</summary>
Ein Element mit <code>id=APP_CONFIG</code> macht <code>window.APP_CONFIG</code> auf
genau dieses Element zeigen. Damit ist der erste Teil der Bedingung truthy.
</details>

<details><summary>💡 Hinweis 3 — die verschachtelte Eigenschaft</summary>
Du brauchst zusätzlich <code>window.APP_CONFIG.debug</code> truthy. Ein
<code>&lt;form&gt;</code> mit passender <code>id</code> und einem benannten Kind-Control
(<code>name=debug</code>) liefert genau diese Verschachtelung: <code>form.debug</code>
zeigt auf das benannte Control. Beide Tags überleben den Sanitizer, weil sie keine
Handler tragen.
</details>

## Was du lernst

- **HTML-Injection ist auch ohne JS gefährlich.** DOM Clobbering überschreibt
  JavaScript-Variablen/-Properties allein über `id`/`name`-Attribute harmloser Tags.
- Warum ein Sanitizer, der „nur Scripts und Handler" entfernt, **nicht** ausreicht.

## Richtig machen

- Sicherheitsrelevante Configs **nicht** aus dem globalen `window`-Namensraum lesen, der
  clobberbar ist. Interne Werte in einem Closure/Modul kapseln.
- Erprobten Sanitizer (DOMPurify) mit Clobbering-Abwehr (`SANITIZE_DOM`) nutzen;
  `id`/`name` auf Nutzer-HTML verbieten.

> Lösung in `loesung.md` — erst nach eigenem Versuch.

## Gelöst
- [ ] Flag gefunden am: `____-__-__`
- Payload:
