# 🎯 CTF — Persönliche Hacking-Übungsumgebung

Deine private Sammlung verwundbarer Labs, geordnet nach **Schwachstellenklasse** und
**Schwierigkeitsstufe**. Alles läuft **rein lokal**, nur zum Lernen und Üben.

> ⚠️ **Nur für lokalen, legalen Gebrauch.** Diese Labs sind absichtlich verwundbar.
> Niemals ins Internet stellen, niemals auf fremde Systeme anwenden. Die Techniken
> hier sind ausschließlich auf deinen eigenen Übungs-Labs erlaubt.

---

## 📁 Ordnerstruktur

```
D:\CTF\
├── README.md                <- diese Datei (Master-Index, für alle Sessions)
├── 01-XSS\
│   ├── README.md            <- Kategorie-Übersicht
│   ├── Stufe-1-Einsteiger\
│   │   └── XSS-01-Reflektierter-Gruss\
│   │       ├── index.html   <- das verwundbare Lab
│   │       ├── README.md    <- Aufgabe + Hinweise
│   │       └── loesung.md   <- Lösung (getrennt, erst nach Versuch öffnen)
│   ├── Stufe-2-Leicht\
│   ├── Stufe-3-Mittel\
│   ├── Stufe-4-Schwer\
│   └── Stufe-5-Experte\
├── 02-SQLi\
│   └── ... (gleiche 5 Stufen)
└── ... (31 Klassen insgesamt)
```

**Prinzip:** `Klasse` → `Stufe` → `einzelnes Lab` (eigener Ordner).

---

## 🪜 Die 5 Schwierigkeitsstufen

| Stufe | Name | Was dich erwartet |
|-------|------|-------------------|
| **1** | Einsteiger | Eine einzige, offensichtliche Lücke. Standard-Payload aus dem Cheatsheet reicht. Kein Filter. Ziel: das **Konzept** kapieren. |
| **2** | Leicht | Lücke klar, aber eine **kleine, naive Hürde** (ein geblocktes Keyword, Groß/Klein). Minimale Payload-Anpassung nötig. |
| **3** | Mittel | **Mehrere Schritte** oder ein nicht-trivialer Filter/Encoding. Du musst den **Kontext** verstehen (z. B. HTML-Attribut vs. JS-String). Etwas Recherche. |
| **4** | Schwer | Realistischer Schutz (Blacklist mit mehreren Regeln, WAF-artig). **Verkettung** mehrerer Erkenntnisse, evtl. zwei Lücken kombinieren. |
| **5** | Experte | Starke Filter / CSP / Framework-Schutz. Angriffsweg nicht offensichtlich. Kreatives **Chaining**, Gadget-Suche, Race o. ä. Nah an echter Bug-Bounty-Härte. |

---

## 🚩 Flag-System

- **Format:** `TELQRR{...}` — z. B. `TELQRR{r3fl3ct3d_xss_1st_d3r_3inst1eg}`
- In **jedem** Lab ist genau eine Flag versteckt.
- **Regel:** Die Flag steht **nie** im offensichtlichen Klartext im Quelltext. Man muss
  die Schwachstelle **tatsächlich ausnutzen**, um sie freizuschalten (z. B. eine
  Funktion aufrufen, eine Datei auslesen, einen Login umgehen). Oft ist sie zusätzlich
  leicht verschleiert (base64, reversed, o. ä.), damit reines "View Source" nicht reicht.
- Gefundene Flags trägst du unten in die **Fortschritts-Tabelle** ein.

---

## 🧩 Aufbau eines Labs (Konvention)

Jedes Lab-Verzeichnis enthält:

| Datei | Zweck |
|-------|-------|
| `index.html` **oder** Server-Code (`app.py`, `server.js`, …) | Das verwundbare Ziel |
| `README.md` | Aufgabenstellung, Ziel, gestufte Hinweise (aufklappbar) |
| `loesung.md` | Vollständige Lösung mit Payload — **getrennt**, damit man nicht spoilert |

**Namensschema der Lab-Ordner:** `KLASSE-NR-Kurzname`, z. B. `XSS-01-Reflektierter-Gruss`,
`SQLi-01-Login-Bypass`. Die Nummer läuft **pro Klasse** hoch, über alle Stufen hinweg.

---

## 🖥️ Setup: Browser-Labs vs. Server-Labs (wichtig, ehrlich)

Nicht alles läuft per Doppelklick — das ist eine technische Realität, keine Bequemlichkeit:

- **🌐 Browser (kein Setup):** `index.html` doppelklicken, fertig. Möglich nur für
  clientseitige Klassen: XSS (Reflected/DOM), Clickjacking, Prototype Pollution.
- **🖥️ Lokaler Server:** braucht einen kleinen laufenden Prozess (Python/Node), weil die
  Lücke **serverseitig** ist (SQLi, IDOR, SSRF, Command-Injection, …). Ohne Server wäre
  die Stufe eine Lüge. Start-Anleitung liegt dann im jeweiligen Lab-README.
- **🔀 Proxy-Setup:** Request-Smuggling / Cache-Poisoning brauchen zusätzlich einen
  vorgelagerten Proxy — die aufwändigsten Setups, kommen zuletzt.

> Sprache/Stack für Server-Labs wird beim ersten Server-Lab festgelegt (Vorschlag:
> **Python + Flask**, weil minimal und ohne Build-Schritt). Bis dahin sind es reine
> Browser-Labs.

---

## 📚 Inventar — alle 31 Schwachstellenklassen

Legende Setup: 🌐 = Browser · 🖥️ = lok. Server · 🔀 = Server + Proxy · 🌐🖥️ = beides je nach Variante

| # | Klasse | Setup | Labs |
|---|--------|:-----:|:----:|
| 01 | XSS — Cross-Site Scripting | 🌐🖥️ | **5** |
| 02 | SQLi — SQL Injection | 🖥️ | 0 |
| 03 | IDOR — Insecure Direct Object References | 🖥️ | 0 |
| 04 | Broken Access Control / PrivEsc | 🖥️ | 0 |
| 05 | Authentication — Login/Session-Schwächen | 🖥️ | 0 |
| 06 | Command Injection | 🖥️ | 0 |
| 07 | Path Traversal / LFI / RFI | 🖥️ | 0 |
| 08 | Unrestricted File Upload | 🖥️ | 0 |
| 09 | SSRF — Server-Side Request Forgery | 🖥️ | 0 |
| 10 | XXE — XML External Entity | 🖥️ | 0 |
| 11 | SSTI — Server-Side Template Injection | 🖥️ | 0 |
| 12 | CSRF — Cross-Site Request Forgery | 🌐🖥️ | 0 |
| 13 | Open Redirect | 🌐🖥️ | 0 |
| 14 | JWT — Token-Angriffe | 🖥️ | 0 |
| 15 | Insecure Deserialization | 🖥️ | 0 |
| 16 | Business Logic Flaws | 🖥️ | 0 |
| 17 | Race Conditions | 🖥️ | 0 |
| 18 | NoSQL Injection | 🖥️ | 0 |
| 19 | CORS Misconfiguration | 🌐🖥️ | 0 |
| 20 | Clickjacking | 🌐 | 0 |
| 21 | Prototype Pollution | 🌐 | 0 |
| 22 | Web Cache Poisoning / Deception | 🔀 | 0 |
| 23 | HTTP Request Smuggling | 🔀 | 0 |
| 24 | Host Header Attacks | 🖥️ | 0 |
| 25 | CRLF Injection / Response Splitting | 🖥️ | 0 |
| 26 | GraphQL API Schwachstellen | 🖥️ | 0 |
| 27 | WebSocket-Schwachstellen | 🖥️ | 0 |
| 28 | LDAP Injection | 🖥️ | 0 |
| 29 | XPath Injection | 🖥️ | 0 |
| 30 | Information Disclosure | 🌐🖥️ | 0 |
| 31 | Cryptographic Failures | 🌐🖥️ | 0 |

---

## ✅ Fortschritt — gelöste Flags

| Lab | Stufe | Datum | Flag |
|-----|-------|-------|------|
| XSS-01-Reflektierter-Gruss | 1 | 2026-09-02 | `TELQRR{r3fl3ct3d_xss_1st_d3r_3inst1eg}` |

<!-- Beim Lösen hier eintragen, z. B.:
| XSS-01-Reflektierter-Gruss | 1 | 2026-09-02 | TELQRR{...} |
-->

---

## 🛠️ Für zukünftige Sessions — so entsteht ein neues Lab

Damit jede Session konsistent weiterbaut, hier die Marschroute:

1. **Klasse + Stufe wählen** (z. B. „SQLi Stufe 1").
2. Ordner anlegen: `D:\CTF\<NN-Klasse>\Stufe-X-Name\<KLASSE-NR-Kurzname>\`
3. **Verwundbares Ziel** bauen (Browser: `index.html`; Server: `app.py` + Start-Doku).
4. **Flag** `TELQRR{...}` verstecken — so, dass nur die echte Ausnutzung sie freilegt;
   zusätzlich leicht verschleiern (base64/reverse), nie Klartext im Quelltext.
5. **README.md** im Lab: Ziel + gestufte, aufklappbare Hinweise (`<details>`).
6. **loesung.md** getrennt schreiben (voller Payload + „richtig-machen"-Teil).
7. Diese **Inventar-Tabelle** und ggf. die **Kategorie-README** aktualisieren
   (Labs-Zähler hochsetzen).
8. Stufen-Definition oben als verbindliche Messlatte für den Schwierigkeitsgrad nehmen.

**Stack-Empfehlung für Server-Labs:** Python 3 + Flask (ein `pip install flask`, ein
`python app.py`, kein Build). Erst festzurren, wenn das erste Server-Lab gebaut wird.

---

## 📜 Changelog

- **2026-09-02** — Grundgerüst: 31 Schwachstellenklassen × 5 Stufen angelegt, Master-README.
- **2026-09-01/02** — Erstes Lab: `XSS-01-Reflektierter-Gruss` (Stufe 1) fertig.
