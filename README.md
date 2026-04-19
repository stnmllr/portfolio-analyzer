# Portfolio Analyzer v5

**Monatlicher KI-Investment-Report — bankenunabhängig, direkt im Browser**

Erstellt jeden Monat einen vollständigen Portfolio-Report auf Basis deiner Depot-CSVs — angereichert mit aktuellen Marktdaten und KI-Analyse nach den Prinzipien von Bridgewater (Ray Dalio), BlackRock und Goldman Sachs.

---

## Live-Demo

**[→ stnmllr.github.io/portfolio-analyzer](https://stnmllr.github.io/portfolio-analyzer/)**

---

## Was das Tool macht

### 1 · Marktlage (Bridgewater-Stil)
- Aktuelle Makro-Einschätzung: EZB/Fed-Zinsen, Inflation, Konjunkturphase
- Daten werden live aus dem Web abgerufen (kein veraltetes Wissen)
- Top-3 Risiken für dein Portfolio diesen Monat

### 2 · Portfolio-Gesundheitscheck
- Asset-Allokation korrekt klassifiziert (Anleihen-ETFs werden anhand Name/WKN/ISIN erkannt, nicht blind als "ETF" geführt)
- Klumpenrisiken: alle Positionen >5% einzeln bewertet
- Geografische Verteilung und USD-Exposure
- Duplikat-Positionen über mehrere Depots
- Liquiditätsbewertung

### 2b · Geografisches Rebalancing (Ray Dalio — Big Debt Cycle)
- USA-Exposure direkt und indirekt via MSCI World berechnet
- Bewertungsvergleich USA vs. Europa (KGV, Shiller P/E, Dividendenrendite)
- Konkrete Europa-ETF-Empfehlungen mit ISIN und TER
- Zeitplan: Wie viele Monate à Sparrate bis Ziel-Quote erreicht?

### 3 · Rebalancing
- IST vs. SOLL Allokation tabellarisch
- Konkrete Kauf-/Verkaufsempfehlungen mit Beträgen
- Steuerliche Überlegungen (Verlustverrechnung nur als Begleithinweis, nie als eigenständige Empfehlung)

### 4 · Diesen Monat investieren
- Konkrete Empfehlung für dein monatliches Budget
- Max. 3 Positionen mit Ticker/ISIN und Begründung
- Indikative Einstiegszonen

### 5 · Stresstest
- Szenario A: -20% Aktienmarktkorrektur
- Szenario B: +20% Tech-Rally
- Größtes Einzelrisiko

### 6 · Entscheidungs-Summary
Eine klare Aktion pro Position — `HALTEN` · `KAUFEN` · `AUFSTOCKEN` · `REDUZIEREN` · `VERKAUFEN` — keine Doppeldeutigkeiten.

---

## Wie es funktioniert

```
01 Setup          → API-Key + Investor-Profil eingeben
02 CSV Upload     → Beliebige Depot-CSVs hochladen (mehrere gleichzeitig möglich)
03 Mapping        → Spalten werden automatisch erkannt und validiert
03b Kontext-Check → KI schlägt Anmerkungen zu Top-Positionen vor — du korrigierst
04 Report         → Vollständiger Monatsreport mit Web-Suche nach aktuellen Marktdaten
```

---

## Voraussetzungen

### Anthropic API-Key
Das Tool benötigt einen eigenen API-Key von Anthropic. Kein claude.ai Pro-Abo nötig.

1. Account erstellen: [console.anthropic.com](https://console.anthropic.com)
2. Unter "API Keys" → "Create Key"
3. Kreditkarte hinterlegen, 5 USD aufladen

**Kosten:** Ca. 3–5 Cent pro vollständiger Analyse (inkl. Web Search). 5 USD reichen für ca. 1–2 Jahre monatlicher Reports.

**Datenschutz:** Der API-Key bleibt ausschließlich lokal im Browser. Er wird nicht gespeichert, nicht übertragen, nicht geloggt.

### CSV-Exporte
Das Tool ist bankenunabhängig und erkennt CSV-Exporte aller gängigen deutschen Broker automatisch:

| Broker | Export-Pfad |
|--------|-------------|
| comdirect | Depot → Tabelle → Export (oben rechts) |
| Commerzbank | Depot → Report → CSV-Download |
| ING | Depot → Depotauszug → CSV |
| DKB | Depot → Export |
| Flatex | Portfolio → Export CSV |

Unterstützte Trennzeichen: `;` · `,` · Tab  
Unterstützte Encodings: UTF-8 · ISO-8859-1 (Latin-1)  
Mehrere Depots gleichzeitig hochladbar.

---

## Kontext-Check — das Herzstück

Vor der Hauptanalyse analysiert Claude deine Top-Positionen und schlägt Anmerkungen vor:

- `A0RGEN (24,5%): Bewusster Stabilitätsanker — Reduktionsempfehlungen ignorieren`
- `LS9AVX (-12%): Verlustposition — nur auflösen falls sowieso Gewinne realisiert werden`
- `Alphabet (7,5%): Klumpenrisiko bekannt — Abbau bei nächster Stärke geplant`

Du siehst jeden Vorschlag als editierbares Textfeld. ✕ entfernt eine Position aus dem Kontext (neutrale Bewertung). Alles was du bestätigst fließt in die Hauptanalyse — Claude weiß dann warum du was hältst.

---

## Lesson Learned

**Anleihen-Klassifikation:** Broker führen Anleihen-ETFs oft als generischen Typ "ETF". Das Tool klassifiziert alle Positionen anhand von Name, WKN und ISIN bevor Allokationsquoten berechnet werden. Beispiel: A0RGEN (iShares EUR Govt Bond ESG) wird korrekt als Anleihen-ETF erkannt, nicht als Aktien-ETF.

**Verlustverrechnung:** Wird nie als eigenständige Verkaufsempfehlung ausgegeben. Nur als Begleithinweis wenn eine Position aus strategischen Gründen sowieso verkauft wird.

---

## Technisches

- Einzelne HTML-Datei, kein Backend, kein Server
- Läuft vollständig im Browser
- Alle Daten bleiben lokal — nur der Analyse-Prompt wird an die Anthropic API gesendet
- Modell: claude-opus-4-5 mit Web Search für aktuelle Marktdaten
- Pre-Analyse (Kontext-Check): claude-sonnet-4-5, ca. 0,5 Cent

---

## Disclaimer

Dieses Tool ist eine KI-gestützte Entscheidungshilfe und stellt **keine Anlageberatung** im Sinne des WpHG dar. Alle Empfehlungen ohne Gewähr. Steuerliche Angaben unverbindlich. Konsultiere vor wesentlichen Anlageentscheidungen einen zugelassenen Finanzberater.

---

## Changelog

| Version | Änderungen |
|---------|-----------|
| v5 | Verlustverrechnung-Fix · Toggle-State-Persistenz im Kontext-Check |
| v4 | Kontext-Check mit KI-Vorschlägen (Schritt 3b) · ✕/+ Toggle ohne Re-Render · Onboarding-Text |
| v3 | Dalio Big Cycle Abschnitt · Europa-Rebalancing-Strategie · Schärfere Entscheidungstabelle |
| v2 | Bankenunabhängiger Multi-CSV-Upload · Auto-Encoding · Mapping mit Ampel-Validierung · Vorschau |
| v1 | Erste Version · comdirect + Commerzbank · Grundanalyse |
