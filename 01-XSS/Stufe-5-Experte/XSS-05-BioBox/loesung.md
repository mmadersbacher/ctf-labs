# 🔓 Lösung — XSS-05 BioBox (DOM Clobbering)

> ⚠️ Spoiler.

## Warum klassisches XSS scheitert

Der Sanitizer entfernt `<script>` und jedes `on*`-Attribut. `<img src=x onerror=...>`
wird zu `<img src=x>`, `<script>...` verschwindet. Es läuft **kein** eigenes JS.

## Der Trick: DOM Clobbering

Der Code prüft `window.APP_CONFIG && window.APP_CONFIG.debug`. Beide Werte lassen sich
allein mit HTML setzen — der Browser legt für Elemente mit `id`/`name` benannte
Zugriffe auf `window` (und auf Formulare) an.

## Payload (in die Bio)

```html
<form id=APP_CONFIG><input name=debug></form>
```

- `id=APP_CONFIG` → `window.APP_CONFIG` zeigt auf das `<form>` (truthy).
- `<input name=debug>` im Form → `form.debug` (= `window.APP_CONFIG.debug`) zeigt auf das
  Input (truthy).
- Beide Tags haben keine Handler/kein Script → sie überleben den Sanitizer unverändert.

Nach `innerHTML` sind die Elemente im Live-DOM → die Bedingung ist wahr → `showFlag()`. ✅

Alternative (ohne form):
```html
<a id=APP_CONFIG></a><a id=APP_CONFIG name=debug></a>
```
Zwei gleiche `id` machen `window.APP_CONFIG` zu einer HTMLCollection; das benannte
Element ist über `.debug` erreichbar.

## 🚩 Flag
```
TELQRR{d0m_cl0bb3r1ng_null_byt3_js}
```

## Richtig machen
Config nicht aus `window` lesen (clobberbar) — in einem Modul/Closure kapseln. Sanitizer
mit Clobbering-Schutz (DOMPurify, `SANITIZE_DOM`), `id`/`name` in Nutzer-HTML verbieten.
