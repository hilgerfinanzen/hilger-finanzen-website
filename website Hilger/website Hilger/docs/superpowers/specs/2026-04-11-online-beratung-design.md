# Design Spec: Online-Beratungsseite (beratung.html)
Stand: 2026-04-11

## Ziel

Neue Subseite `beratung.html` für die Hilger Versicherungen Website. Die Seite bietet digitale Online-Beratung mit KI-Chatbot, Bedarfs-Schnellcheck und Terminbuchung — und kommuniziert explizit die deutschlandweite / DACH-weite Verfügbarkeit.

---

## Entscheidungen

| Thema | Entscheidung |
|---|---|
| Chatbot-Typ | Statisch (vorprogrammierte Antworten, kein Backend, kein API-Key) |
| Features | Bedarfs-Schnellcheck + Terminbuchung + KI-Chatbot |
| Layout | Zwei Spalten: linke Spalte Inhalte, rechte Spalte Chatbot fix |
| Integration | Neue Datei `beratung.html`, Nav-Link in `index.html` ergänzen |
| Stil | Identisch zur Hauptseite: Navy/Cream/Copper, Cormorant Garamond + DM Sans, Alpine.js |

---

## Architektur

```
index.html          (bestehend — Nav-Link "Beratung" hinzufügen)
beratung.html       (neu — Subseite)
```

Keine externen Abhängigkeiten über die bereits in `index.html` vorhandenen hinaus (Google Fonts, Alpine.js CDN).

---

## Seitenstruktur

### 1. Navigation
- Identisch zur Hauptseite (copy + leichte Anpassung)
- Aktiver Link: "Beratung" hervorgehoben (copper-light)

### 2. Hero-Bereich
- Überschrift: *"Beratung ohne Grenzen — persönlich, digital, überall."*
- Subtext: Hinweis auf ortsunabhängige Beratung
- DACH-Strip: Flaggen 🇩🇪🇦🇹🇨🇭 + Text *"Deutschlandweit & DACH-weit verfügbar — 100 % digital"*
- Hintergrund: Navy mit Copper-Glow (wie Hauptseite)

### 3. Haupt-Layout (zwei Spalten)

**Linke Spalte (flex: 1)**

**Bedarfs-Schnellcheck**
- 4 Fragen, eine nach der anderen (Alpine.js-gesteuert)
- Fortschrittsbalken oben (4 Segmente)
- Fragen:
  1. Für wen suchen Sie Versicherungsschutz? (KMU / Privat / Beides)
  2. Größter Absicherungsbedarf? (Betriebshaftpflicht & Cyber / BAV / Rechtsschutz / Unsicher)
  3. Wie viele Mitarbeiter hat Ihr Unternehmen? (1–10 / 11–50 / 51–100 / 100+)
  4. Wie dringend ist Ihre Anfrage? (Sofort / In den nächsten Wochen / Nur informieren)
- Abschluss: Kurze Empfehlung + CTA "Jetzt Termin buchen"

**Terminbuchung**
- Angezeigt nach Abschluss des Schnellchecks (oder direkt zugänglich per Scroll)
- 6 Zeitslots in 2×3-Grid (statische Beispieldaten)
- Slot auswählbar (Alpine.js toggle)
- Bestätigungsbutton: *"Termin bestätigen – kostenlos & unverbindlich"*
- Hinweis: Slots sind Demo; echter Betrieb via Calendly-Link oder mailto erweiterbar

**Rechte Spalte (320px fest)**

**Chatbot-Panel**
- Header: Avatar "KI", Name "Hilger KI-Berater", Status "Online · Antwortet sofort"
- Nachrichten-Feed: scrollbar, max-height begrenzt
- Vorprogrammierte Antworten via Alpine.js (keyword-basiert):
  - Begrüßung (automatisch beim Laden)
  - DACH-Hinweis in zweiter Nachricht
  - Keywords: "cyber", "haftpflicht", "altersvorsorge", "termin", "kosten", "privat"
  - Fallback: "Ich leite Sie gerne an einen unserer Experten weiter."
- Schnellauswahl-Buttons: Betriebshaftpflicht / Altersvorsorge / Termin buchen / Kosten & Preise
- Eingabefeld + Senden-Button

---

## Chatbot-Antworten (statisch, vollständige Liste)

| Trigger-Keyword | Antwort |
|---|---|
| (Begrüßung) | "Guten Tag! Ich bin Ihr digitaler Berater von Hilger Versicherungen. Wie kann ich Ihnen helfen?" |
| (2. Auto-Nachricht) | "Übrigens: Wir beraten Sie vollständig digital — ob München, Hamburg, Wien oder Zürich. 🇩🇪🇦🇹🇨🇭" |
| cyber | "Für Cyberversicherungen sind Unternehmensgröße und IT-Infrastruktur entscheidend. Wie viele Mitarbeiter hat Ihr Unternehmen?" |
| haftpflicht | "Betriebshaftpflicht ist für KMU unverzichtbar. Ich empfehle unseren Schnellcheck — er dauert nur 2 Minuten." |
| altersvorsorge | "Betriebliche Altersvorsorge ist steuerlich attraktiv und ein starkes Mitarbeiterbindungs-Tool. Soll ich Ihnen mehr erklären?" |
| termin | "Gerne! Wählen Sie einfach einen Zeitslot links aus — kostenlos und unverbindlich." |
| kosten / preis | "Unsere Erstberatung ist kostenlos. Die Versicherungskosten hängen von Ihrem Bedarf ab — der Schnellcheck hilft uns, ein passendes Angebot zu erstellen." |
| privat | "Für Privatkunden bieten wir Haftpflicht, Krankenversicherung, Berufsunfähigkeit und Altersvorsorge. Was interessiert Sie?" |
| (Fallback) | "Das beantworte ich gerne im persönlichen Gespräch. Vereinbaren Sie einfach einen kostenlosen Termin!" |

---

## Technische Details

- **Framework:** Alpine.js (bereits in index.html, gleiche CDN-URL)
- **State:** `x-data` Objekte pro Sektion (Schnellcheck-Schritt, ausgewählter Slot, Chat-Nachrichten)
- **Keine externe API**, kein Backend
- **Responsive:** Unter 768px wechselt das Grid auf eine Spalte; Chatbot rutscht unter den Content
- **CSS:** CSS-Variablen aus index.html übernehmen (navy, copper, cream etc.)

---

## Was NICHT enthalten ist (bewusst ausgelassen)

- Echte Kalender-/Buchungslogik (Erweiterung via Calendly möglich)
- KI-Backend / Claude API (bewusst statisch gehalten)
- Kontaktformular (nicht gewünscht)
- DACH-Karte als Grafik (nicht gewünscht)
