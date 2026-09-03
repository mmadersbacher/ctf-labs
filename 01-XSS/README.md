# XSS — Cross-Site Scripting

**Kategorie:** Cross-Site Scripting (Reflected, Stored, DOM-basiert)
**Setup:** 🌐 Browser (DOM/Reflected) · 🖥️ Server (Stored) — je nach Lab
**Flag-Format:** TELQRR{...}

## Worum geht es

Angreifer schleusen JavaScript ein, das im Browser des **Opfers** im Kontext der
Zielseite ausgeführt wird. Damit lassen sich Sessions/Cookies stehlen, Aktionen im
Namen des Opfers ausführen oder die Seite manipulieren. Drei Hauptvarianten:

- **Reflected:** Eingabe kommt (z. B. über die URL) sofort ungefiltert zurück in die Seite.
- **Stored:** Eingabe wird gespeichert und später bei anderen Nutzern ausgeliefert.
- **DOM-basiert:** Clientseitiges JS schreibt Eingabe unsicher ins DOM.

## Labs in dieser Kategorie

| Stufe | Ordner | Lab | Setup |
|-------|--------|-----|-------|
| **1** Einsteiger | Stufe-1-Einsteiger/ | **XSS-01-Reflektierter-Gruss** — `innerHTML` ohne Filter, Payload schaltet die Flag frei | 🌐 Browser |
| **2** Leicht | Stufe-2-Leicht/ | **XSS-02-Suche-mit-Filter** — Blacklist-Filter mit Case- & Non-Recursion-Bug, muss umgangen werden | 🌐 Browser |
| **3** Mittel | Stufe-3-Mittel/ | **XSS-03-ProfilCard** | 🌐 Browser |
| **4** Schwer | Stufe-4-Schwer/ | **XSS-04-QuickNote** | 🌐 Browser |
| **5** Experte | Stufe-5-Experte/ | **XSS-05-BioBox** | 🌐 Browser |

## Status

**5 Labs** vorhanden — XSS-Klasse über alle 5 Stufen komplett. 🎉

---
Zurueck zur Uebersicht: ../README.md
