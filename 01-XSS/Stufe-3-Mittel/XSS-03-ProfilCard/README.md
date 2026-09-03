# XSS-03 — ProfilCard

| | |
|---|---|
| **Schwachstelle** | Cross-Site Scripting (XSS) im Attribut-Kontext |
| **Stufe** | 3 — Mittel |
| **Ziel** | Eigenes JS ausführen und `showFlag()` triggern |
| **Flag-Format** | `TELQRR{...}` |
| **Setup** | Keins. `index.html` im Browser öffnen. |

## Die Story

Die ProfilCard übernimmt deinen Anzeigenamen in die Karte. Anders als in XSS-01/02
landet dein Text diesmal **nicht** einfach im Seitenkörper — schau genau hin, **wohin**
er geschrieben wird. Dein bewährter `<img ...>`-Payload erscheint plötzlich nur als
sichtbarer Text im Feld und feuert nicht. Warum?

## Auftrag

1. Öffne `index.html`, gib deinen alten Payload ein — er landet als Text im Kartenfeld.
2. **Lies den Quelltext** (Funktion `renderCard`) und erkenne, in welchem **Kontext**
   dein Name eingesetzt wird.
3. Passe den Payload so an, dass er aus diesem Kontext ausbricht, und triggere `showFlag()`.

## Hinweise

<details><summary>💡 Hinweis 1</summary>
Schau dir an, wie <code>renderCard</code> deinen Namen einbaut. Dein Text steht mitten
in einem HTML-Element — aber nicht als Inhalt, sondern als Wert eines Attributs.
</details>

<details><summary>💡 Hinweis 2</summary>
Solange du innerhalb des Attribut-Werts bleibst, ist dein Text nur Text. Du musst den
Attribut-Wert und das umgebende Tag zuerst <b>schließen</b>, bevor du ein eigenes Tag
beginnen kannst. Welche zwei Zeichen schließen ein <code>value="..."</code> und danach
das Tag?
</details>

<details><summary>💡 Hinweis 3</summary>
Erst ausbrechen, dann wie in XSS-01 ein Tag mit selbst-feuerndem Handler anhängen.
</details>

## Was du lernst

- **HTML-Kontext ist alles.** Dieselbe Eingabe ist harmlos oder tödlich, je nachdem ob
  sie im Element-Body, in einem Attribut, in einem `<script>` oder in einer URL landet.
- Attribut-Ausbruch: erst den Wert und das Tag schließen, dann injizieren.

## Richtig machen

- Attribut-Werte **kontext-korrekt encoden** (`"` → `&quot;`), oder Werte über
  `element.setAttribute('value', name)` / `.value = name` setzen statt via String-`innerHTML`.

> Lösung in `loesung.md` — erst nach eigenem Versuch.

## Gelöst
- [ ] Flag gefunden am: `____-__-__`
- Payload:
