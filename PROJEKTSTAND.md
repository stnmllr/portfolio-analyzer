# Portfolio Analyzer — Projektstand 19.04.2026

## Aktueller Stand

Die HTML-Version (v5.3) ist produktiv auf GitHub Pages deployed und funktioniert für den monatlichen Eigengebrauch.

**URL:** https://stnmllr.github.io/portfolio-analyzer/

**Stack:** Einzelne HTML-Datei · Anthropic API (claude-opus-4-5 + Web Search) · GitHub Pages

---

## Was funktioniert (v5.3)

- Bankenunabhängiger CSV-Upload mit Auto-Encoding (UTF-8 / ISO-8859-1)
- Automatisches Spalten-Mapping mit Plausibilitätsprüfung (Ampel-Validierung)
- Korrekte Anleihen-Klassifikation anhand Name/WKN/ISIN (nicht blind nach Broker-Typ)
- Kontext-Check: KI schlägt Anmerkungen zu Top-Positionen vor, editierbar vor Analyse
- 7-Abschnitte-Report mit aktuellen Marktdaten via Web Search
- Dalio Big Cycle Analyse (USA vs. Europa, Option A+B)
- Sparplan-Berücksichtigung (optionales Eingabefeld)
- Entscheidungs-Summary mit einer klaren Aktion pro Position
- Verlustverrechnung korrekt: nie eigenständige Empfehlung, nur Begleithinweis
- Druckbares PDF via window.print()

---

## Bekannte Schwächen der HTML-Version

### Rendering
- Claude produziert manchmal inkonsistentes Markdown (Zitate zwischen Tabellenzeilen)
- Renderer ist robuster geworden, aber bei schlechtem Claude-Output noch sichtbar
- Kein deterministisches Output-Format — jeder API-Call leicht anders

### Konsistenz
- Prioritäts-Darstellung variiert (Sterne / Ampeln / Zahlen) — Claude erfindet jeweils eigenes Format
- Strategie-Einschätzung variiert zwischen Läufen (temperature > 0, verschiedene Web-Search-Ergebnisse)
- Keine Versionierung von Prompt-Varianten

### Persistenz
- Kein Speicher — jeder Monat von vorne (Profil, Kontext, Sparpläne neu eingeben)
- Keine Delta-Erkennung (was hat sich seit letztem Monat geändert?)
- Kein automatischer Versand

### Skalierung
- Funktioniert gut für 1-2 Nutzer mit eigenem API-Key
- Nicht skalierbar für viele Nutzer ohne zentrales Backend

---

## Nächste Entwicklungsstufe: Backend-Version

### Konzept

Statt freiem Markdown-Output strukturiertes JSON vom Modell — serverseitig gerendert, persistent gespeichert, monatlich per Email verschickt.

### Architektur

```
Browser (HTML/JS)
    │
    ▼
Cloud Run (FastAPI — Python)
    ├── Anthropic API (strukturierter JSON-Prompt)
    ├── Firestore (Persistenz: Portfolio, Kontext, Sparpläne, Reports)
    └── SendGrid / Gmail API (Email-Versand)
```

**GCP-Projekt:** boxwood-mantra-489408-c0 (bereits vorhanden, Telefon-Agent läuft dort)
**Region:** europe-west3

### Kernkomponenten

**1. Portfolio-Service**
- CSV-Upload und Harmonisierung (bankenunabhängig, aktuell in JS — nach Python portieren)
- Persistenz: letzte bekannte Portfolio-Positionen in Firestore speichern
- Delta-Erkennung: was hat sich seit letztem Monat geändert?

**2. Analyse-Service**
- Strukturierter JSON-Prompt statt Markdown
- Claude gibt zurück:
```json
{
  "marktlage": [...],
  "allokation": [...],
  "klumpen": [...],
  "empfehlungen": [
    {"position": "A0RGEN", "aktion": "REDUZIEREN", "betrag": 20000, "prioritaet": 1, "begruendung": "..."}
  ],
  "stresstest": {...},
  "summary": [...]
}
```
- Server rendert JSON → sauberes HTML (kein Markdown-Parsing-Problem mehr)
- Deterministisch: gleiches Portfolio → gleiche Struktur

**3. Persistenz (Firestore)**
```
users/{userId}/
  profile          → Alter, Budget, Risiko, Ziel, Horizont
  sparplaene       → bestehende Sparpläne
  kontext          → Positions-Anmerkungen aus Kontext-Check
  portfolios/      → monatliche CSV-Snapshots
  reports/         → generierte Reports mit Timestamp
```

**4. Email-Service**
- Monatlicher Cron-Job (Cloud Scheduler) triggert Analyse
- Report wird als HTML-Email verschickt (SendGrid oder Gmail API)
- Nutzer lädt neue CSVs nur hoch wenn sich Portfolio geändert hat
- Sonst: automatische Analyse auf Basis letzter bekannter Daten + aktuelle Marktlage

### Neuer User-Flow

```
Erstmalig:
  → Registrierung (Email + API-Key)
  → Profil anlegen
  → CSVs hochladen + Mapping bestätigen
  → Kontext-Check
  → Erster Report

Monatlich (Folgemonate):
  → Email mit Report kommt automatisch
  → Optional: neue CSVs hochladen wenn sich etwas geändert hat
  → Optional: Kontext anpassen

Bei Portfolio-Änderung:
  → Neue CSV hochladen
  → Delta wird angezeigt (neue Positionen / weggefallene Positionen)
  → Kontext-Check nur für geänderte Positionen
```

### Technischer Stack

| Komponente | Technologie |
|-----------|-------------|
| Backend | Python 3.11 · FastAPI |
| Hosting | Google Cloud Run (europe-west3) |
| Persistenz | Firestore |
| Auth | Firebase Auth oder einfaches Token-System |
| Email | SendGrid API oder Gmail API |
| Scheduler | Cloud Scheduler (monatlicher Cron) |
| Frontend | Bestehende HTML-Basis, angepasst für Backend-Calls |
| CI/CD | GitHub Actions (wie Telefon-Agent) |

### Aufwand-Schätzung

| Phase | Inhalt | Aufwand |
|-------|--------|---------|
| 1 | CSV-Harmonisierung in Python portieren | ~2h |
| 2 | FastAPI Backend mit Anthropic-Call (JSON-Prompt) | ~3h |
| 3 | Firestore-Persistenz (Profil, Portfolio, Reports) | ~2h |
| 4 | Cloud Run Deployment + GitHub Actions CI/CD | ~1h |
| 5 | Email-Versand (SendGrid) | ~2h |
| 6 | Frontend anpassen (Backend-Calls statt direkte API) | ~2h |
| 7 | Cron-Job + automatischer monatlicher Versand | ~1h |
| **Gesamt** | | **~13h / 3-4 Sessions** |

### Offene Fragen vor Start

- API-Key-Strategie: Jeder Nutzer bringt eigenen Key mit (wie jetzt) oder zentraler Key im Backend?
- Auth: Einfaches Token per Email oder vollständige Firebase Auth?
- Email-Provider: SendGrid (kostenlos bis 100 Mails/Tag) oder Gmail API?
- Multi-User: Nur für dich und Freunde (whitelist) oder offen?

---

## Lessons Learned (HTML-Version)

1. **Anleihen-Klassifikation zuerst** — Broker-Typ ist unzuverlässig, immer Name/WKN/ISIN prüfen
2. **`type="button"` immer explizit** — ohne Attribut ist jeder Button ein Submit-Button
3. **Unclosed divs crashen den DOM-Parser** — Browser verwirft alles danach stillschweigend
4. **CSP-Meta-Tags mit Vorsicht** — können auf GitHub Pages unerwartete Seiteneffekte haben
5. **Markdown-Rendering ist fragil** — strukturiertes JSON wäre der richtige Ansatz
6. **Verlustverrechnung niemals eigenständig empfehlen** — nur als Begleithinweis
7. **Viele Patches → inkonsistenter Dateizustand** — Datei regelmäßig neu aufbauen statt endlos patchen
