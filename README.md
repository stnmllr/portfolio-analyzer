# Portfolio Analyzer v5.2

**Monatlicher KI-Investment-Report — bankenunabhängig, direkt im Browser**

Erstellt jeden Monat einen vollständigen Portfolio-Report auf Basis deiner Depot-CSVs — angereichert mit aktuellen Marktdaten und KI-Analyse nach den Prinzipien von Bridgewater (Ray Dalio), BlackRock und Goldman Sachs.

---

## Live-Demo

**[→ stnmllr.github.io/portfolio-analyzer](https://stnmllr.github.io/portfolio-analyzer/)**

---

## Was das Tool macht

### 1 · Marktlage (Bridgewater-Stil)
Aktuelle Makro-Einschätzung mit Live-Daten: EZB/Fed-Zinsen, Inflation, Konjunkturphase, Top-3 Risiken.

### 2 · Portfolio-Gesundheitscheck
Asset-Allokation korrekt klassifiziert, Klumpenrisiken, USD-Exposure, Duplikat-Positionen.

### 2b · Geografisches Rebalancing (Ray Dalio — Big Debt Cycle)
USA-Exposure berechnet, KGV-Vergleich USA vs. Europa, Europa-ETF-Empfehlung, Option A+B.

### 3 · Rebalancing
IST vs. SOLL, konkrete Empfehlungen. Verlustverrechnung nur als Begleithinweis — nie eigenständig.

### 4 · Diesen Monat investieren
Konkrete Empfehlung für monatliches Budget mit ISIN und Einstiegszone.

### 5 · Stresstest
-20% Korrektur und +20% Tech-Rally Szenarien.

### 6 · Entscheidungs-Summary
Teil A: Eine klare Aktion pro Position — HALTEN · AUFSTOCKEN · REDUZIEREN · VERKAUFEN.
Teil B: Neue Kaufempfehlungen mit ISIN und EUR/Monat.

---

## Flow

```
01 Setup          → API-Key + Investor-Profil
02 CSV Upload     → Beliebige Depot-CSVs (mehrere gleichzeitig)
03 Mapping        → Automatische Spaltenerkennung + Validierung
03b Kontext-Check → KI schlägt Anmerkungen zu Top-Positionen vor
04 Report         → Vollständiger Report mit aktuellen Marktdaten
```

---

## Voraussetzungen

### Anthropic API-Key
1. Account erstellen: [console.anthropic.com](https://console.anthropic.com)
2. "API Keys" → "Create Key"
3. 5–10 USD aufladen → reicht für ca. 2 Jahre monatlicher Reports (3–8 Cent pro Analyse)

### CSV-Exporte

| Broker | Export-Pfad |
|--------|-------------|
| comdirect | Depot → Tabelle → Export |
| Commerzbank | Depot → Report → CSV-Download |
| ING | Depot → Depotauszug → CSV |
| DKB | Depot → Export |
| Flatex | Portfolio → Export CSV |

Unterstützte Trennzeichen: `;` · `,` · Tab — Encodings: UTF-8 · ISO-8859-1

---

## Kontext-Check

KI schlägt editierbare Anmerkungen zu Top-Positionen vor — du korrigierst, dann startet die Analyse mit diesem Kontext. ✕ entfernt eine Position aus dem Kontext (neutrale Bewertung).

---

## Disclaimer

KI-gestützte Entscheidungshilfe — **keine Anlageberatung** im Sinne des WpHG.

---

## Changelog

| Version | Änderungen |
|---------|-----------|
| **v5.2** | Bugfix: fehlendes `</div>` · `type="button"` für alle Buttons · Verlustverrechnungs-Fix |
| v5.1 | UI-Vereinfachung · Changelog kollabiert · Feature-Karten entfernt |
| v4 | Kontext-Check · ✕/+ Toggle · Onboarding |
| v3 | Dalio Big Cycle · Europa-Rebalancing · Option A+B |
| v2 | Bankenunabhängig · Multi-CSV · Mapping mit Validierung |
| v1 | Erste Version |
