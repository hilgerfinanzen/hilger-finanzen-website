# Online-Beratungsseite Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Neue Subseite `beratung.html` mit zweispaltigem Layout: linke Spalte mit DACH-Hero, Bedarfs-Schnellcheck (4 Fragen, Alpine.js) und Terminbuchung; rechte Spalte mit statischem KI-Chatbot (keyword-basiert, Alpine.js). Nav-Link in `index.html` aktualisieren.

**Architecture:** Eigenständige HTML-Datei, die dieselben CSS-Variablen, Fonts und Alpine.js-Version wie `index.html` verwendet. Kein Backend, keine externe API. Alpine.js verwaltet drei unabhängige Komponenten: Schnellcheck-State, Slot-Auswahl-State und Chat-State.

**Tech Stack:** HTML5, CSS3 (Custom Properties), Alpine.js 3.14.1 (CDN), Google Fonts (Cormorant Garamond + DM Sans)

---

## Datei-Übersicht

| Aktion | Datei | Verantwortlichkeit |
|---|---|---|
| Create | `beratung.html` | Vollständige Beratungsseite |
| Modify | `index.html` (Zeile 358, 372) | Nav-Link auf `beratung.html` umbiegen |

---

## Task 1: Grundgerüst + Nav

**Files:**
- Create: `beratung.html`

- [ ] **Schritt 1: Datei anlegen mit Head, CSS-Basis und Navigation**

Erstelle `D:/Claude Code/website Hilger/beratung.html` mit folgendem Inhalt:

```html
<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Online-Beratung – Hilger Versicherungen</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;1,300;1,400&family=DM+Sans:opsz,wght@9..40,300;9..40,400;9..40,500;9..40,600&display=swap" rel="stylesheet">
  <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.14.1/dist/cdn.min.js"></script>
  <style>
    :root {
      --navy: #0B1D2E;
      --navy-mid: #122640;
      --cream: #F4EFE6;
      --cream-dark: #EDE7DC;
      --copper: #C07830;
      --copper-light: #D4922A;
      --text: #1C2B3A;
      --muted: #6B7A8D;
      --light: #F9F6F2;
    }
    *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
    html { scroll-behavior: smooth; }
    body { font-family: 'DM Sans', sans-serif; color: var(--text); background: var(--navy); overflow-x: hidden; }
    h1, h2, h3 { font-family: 'Cormorant Garamond', serif; }

    /* NAV */
    nav {
      position: fixed; top: 0; left: 0; right: 0; z-index: 100;
      padding: 1.25rem 2rem;
      display: flex; align-items: center; justify-content: space-between;
      transition: all 0.4s ease;
      background: rgba(11,29,46,0.97);
      backdrop-filter: blur(12px);
      box-shadow: 0 4px 30px rgba(0,0,0,0.2);
    }
    .nav-logo { display: flex; flex-direction: column; line-height: 1; text-decoration: none; }
    .nav-logo-name { font-family: 'Cormorant Garamond', serif; font-size: 1.5rem; font-weight: 600; color: white; letter-spacing: 0.02em; }
    .nav-logo-sub { font-size: 0.65rem; color: var(--copper-light); letter-spacing: 0.15em; text-transform: uppercase; }
    .nav-links { display: flex; gap: 2.5rem; list-style: none; }
    .nav-links a {
      color: rgba(255,255,255,0.75); text-decoration: none; font-size: 0.875rem;
      transition: color 0.2s; position: relative;
    }
    .nav-links a::after {
      content: ''; position: absolute; bottom: -4px; left: 0; right: 100%; height: 1px;
      background: var(--copper-light); transition: right 0.3s ease;
    }
    .nav-links a:hover { color: white; }
    .nav-links a:hover::after { right: 0; }
    .nav-links a.active { color: var(--copper-light); }
    .nav-links a.active::after { right: 0; }
    .btn-primary {
      background: var(--copper); color: white; padding: 0.625rem 1.5rem;
      border-radius: 2px; font-size: 0.875rem; font-weight: 500; text-decoration: none;
      transition: all 0.2s; border: none; cursor: pointer; letter-spacing: 0.03em;
      font-family: 'DM Sans', sans-serif;
    }
    .btn-primary:hover { background: var(--copper-light); transform: translateY(-1px); box-shadow: 0 8px 20px rgba(192,120,48,0.3); }

    /* MOBILE NAV */
    .nav-hamburger { display: none; flex-direction: column; gap: 5px; cursor: pointer; }
    .nav-hamburger span { display: block; width: 24px; height: 1.5px; background: white; transition: all 0.3s; }
    .mobile-menu {
      position: fixed; inset: 0; background: var(--navy); z-index: 200;
      display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 2rem;
    }
    .mobile-menu a { font-family: 'Cormorant Garamond', serif; font-size: 2.25rem; color: white; text-decoration: none; }
    .mobile-close {
      position: absolute; top: 1.5rem; right: 1.75rem;
      background: none; border: none; color: white; font-size: 1.5rem; cursor: pointer;
    }

    @media (max-width: 640px) {
      .nav-links, .nav-cta { display: none; }
      .nav-hamburger { display: flex; }
    }
  </style>
</head>
<body x-data="{ menuOpen: false }">

  <nav>
    <a href="index.html" class="nav-logo">
      <span class="nav-logo-name">Hilger</span>
      <span class="nav-logo-sub">Versicherungen</span>
    </a>
    <ul class="nav-links">
      <li><a href="index.html#leistungen">Leistungen</a></li>
      <li><a href="beratung.html" class="active">Online-Beratung</a></li>
      <li><a href="index.html#usp">Über uns</a></li>
      <li><a href="index.html#kontakt">Kontakt</a></li>
    </ul>
    <a href="#termin" class="btn-primary nav-cta">Termin buchen</a>
    <div class="nav-hamburger" @click="menuOpen = true">
      <span></span><span></span><span></span>
    </div>
  </nav>

  <div class="mobile-menu" x-show="menuOpen" x-transition>
    <button class="mobile-close" @click="menuOpen = false">✕</button>
    <a href="index.html#leistungen" @click="menuOpen = false">Leistungen</a>
    <a href="beratung.html"         @click="menuOpen = false">Online-Beratung</a>
    <a href="index.html#usp"        @click="menuOpen = false">Über uns</a>
    <a href="index.html#kontakt"    @click="menuOpen = false">Kontakt</a>
    <a href="#termin" @click="menuOpen = false" class="btn-primary" style="font-size:1rem;padding:.875rem 2rem">Termin buchen</a>
  </div>

  <!-- Inhalt folgt in nächsten Tasks -->

</body>
</html>
```

- [ ] **Schritt 2: Im Browser prüfen**

Öffne `beratung.html` direkt im Browser (Doppelklick oder Live Server).
Erwartetes Ergebnis:
- Navy-Hintergrund
- Fixe Nav mit "Hilger Versicherungen" Logo
- "Online-Beratung" Link ist copper-farbig (aktiv) hervorgehoben
- Auf < 640px: Hamburger-Menü erscheint, Links verschwinden

- [ ] **Schritt 3: Commit**

```bash
cd "D:/Claude Code/website Hilger"
git add beratung.html
git commit -m "feat: add beratung.html base structure with nav"
```

---

## Task 2: Hero-Bereich mit DACH-Botschaft

**Files:**
- Modify: `beratung.html` (nach `<!-- Inhalt folgt in nächsten Tasks -->`)

- [ ] **Schritt 1: CSS für Hero ergänzen**

Füge folgendes CSS in das `<style>`-Tag von `beratung.html` ein (vor dem schließenden `</style>`):

```css
/* HERO */
.hero {
  min-height: 52vh;
  background: var(--navy);
  position: relative;
  display: flex; align-items: center;
  overflow: hidden;
  padding-top: 5rem;
}
.hero-glow {
  position: absolute; top: -20%; right: -10%; width: 60%; height: 140%;
  background: radial-gradient(ellipse, rgba(192,120,48,0.09) 0%, transparent 70%);
  pointer-events: none;
}
.hero-pattern {
  position: absolute; inset: 0; opacity: 0.03;
  background-image: repeating-linear-gradient(45deg, transparent, transparent 40px, rgba(255,255,255,0.5) 40px, rgba(255,255,255,0.5) 41px);
}
.hero-inner {
  position: relative; z-index: 2;
  max-width: 1200px; margin: 0 auto;
  padding: 4rem 2rem 3.5rem; width: 100%;
}
.hero-tag {
  display: inline-flex; align-items: center; gap: 0.5rem;
  font-size: 0.72rem; letter-spacing: 0.15em; text-transform: uppercase;
  color: var(--copper-light); font-weight: 500; margin-bottom: 1.25rem;
}
.hero-tag::before { content: ''; display: block; width: 24px; height: 1px; background: var(--copper-light); }
.hero-h1 {
  font-size: clamp(2.25rem, 4vw, 3.75rem); font-weight: 400; line-height: 1.08;
  color: white; margin-bottom: 1rem; letter-spacing: -0.01em;
}
.hero-h1 em { font-style: italic; font-weight: 300; color: var(--copper-light); }
.hero-sub { font-size: 1rem; line-height: 1.75; color: rgba(255,255,255,0.6); margin-bottom: 2rem; font-weight: 300; max-width: 540px; }
.dach-strip {
  display: inline-flex; align-items: center; gap: 0.75rem;
  background: rgba(192,120,48,0.08); border: 1px solid rgba(192,120,48,0.25);
  border-radius: 4px; padding: 0.75rem 1.25rem; flex-wrap: wrap;
}
.dach-flags { font-size: 1.25rem; letter-spacing: 0.15em; }
.dach-text { font-size: 0.8rem; color: rgba(255,255,255,0.7); line-height: 1.45; }
.dach-text strong { color: var(--copper-light); display: block; }
```

- [ ] **Schritt 2: Hero-HTML einfügen**

Ersetze den Kommentar `<!-- Inhalt folgt in nächsten Tasks -->` durch:

```html
  <!-- HERO -->
  <section class="hero">
    <div class="hero-pattern"></div>
    <div class="hero-glow"></div>
    <div class="hero-inner">
      <div class="hero-tag">Online-Beratung</div>
      <h1 class="hero-h1">Beratung ohne <em>Grenzen</em> —<br>persönlich, digital, überall.</h1>
      <p class="hero-sub">Unsere Experten beraten Sie unabhängig von Ihrem Standort. Ob München, Berlin oder Zürich — echte Bedarfsanalyse, wo immer Sie sind.</p>
      <div class="dach-strip">
        <span class="dach-flags">🇩🇪 🇦🇹 🇨🇭</span>
        <div class="dach-text">
          <strong>Deutschlandweit &amp; DACH-weit verfügbar</strong>
          100 % digital — kein Ortstermin notwendig
        </div>
      </div>
    </div>
  </section>

  <!-- MAIN CONTENT (folgt) -->
```

- [ ] **Schritt 3: Im Browser prüfen**

Erwartetes Ergebnis:
- Navy-Hero mit Copper-Glow rechts
- Großer Titel mit kursivem Copper-Wort "Grenzen"
- DACH-Strip mit Flaggen und Text sichtbar
- Kein Layout-Bruch

- [ ] **Schritt 4: Commit**

```bash
git add beratung.html
git commit -m "feat: add hero section with DACH messaging"
```

---

## Task 3: Zweispaltiges Haupt-Layout

**Files:**
- Modify: `beratung.html`

- [ ] **Schritt 1: CSS für das Grid-Layout ergänzen**

Füge in das `<style>`-Tag ein (vor `</style>`):

```css
/* MAIN LAYOUT */
.main-layout {
  display: grid;
  grid-template-columns: 1fr 340px;
  min-height: calc(100vh - 52vh);
  background: var(--navy);
}
.col-left {
  padding: 3rem 2.5rem;
  border-right: 1px solid rgba(255,255,255,0.06);
}
.col-right {
  position: sticky;
  top: 0;
  height: 100vh;
  display: flex;
  flex-direction: column;
  background: #0d1f2f;
}

/* SECTION HEADERS (shared) */
.s-tag {
  display: inline-flex; align-items: center; gap: 0.5rem;
  font-size: 0.68rem; letter-spacing: 0.15em; text-transform: uppercase;
  color: var(--copper); font-weight: 500; margin-bottom: 0.6rem;
}
.s-tag::before { content: ''; display: block; width: 16px; height: 1px; background: var(--copper); }
.s-title {
  font-size: clamp(1.5rem, 2.5vw, 2.25rem); font-weight: 400; line-height: 1.15;
  color: white; margin-bottom: 1.75rem; letter-spacing: -0.01em;
}
.s-title em { font-style: italic; color: var(--copper-light); }

.section-divider {
  height: 1px; background: rgba(255,255,255,0.07); margin: 2.5rem 0;
}

/* RESPONSIVE */
@media (max-width: 860px) {
  .main-layout { grid-template-columns: 1fr; }
  .col-right { position: static; height: auto; min-height: 480px; }
  .col-left { padding: 2rem 1.25rem; border-right: none; border-bottom: 1px solid rgba(255,255,255,0.06); }
}
```

- [ ] **Schritt 2: Layout-Skeleton ins HTML einfügen**

Ersetze `<!-- MAIN CONTENT (folgt) -->` durch:

```html
  <!-- MAIN: Two-column -->
  <div class="main-layout">

    <!-- LINKE SPALTE -->
    <div class="col-left">
      <!-- Schnellcheck (folgt Task 4) -->
      <!-- Terminbuchung (folgt Task 5) -->
    </div>

    <!-- RECHTE SPALTE: CHAT -->
    <div class="col-right">
      <!-- Chatbot (folgt Task 6) -->
    </div>

  </div>
```

- [ ] **Schritt 3: Im Browser prüfen**

Erwartetes Ergebnis:
- Unter dem Hero: zwei Spalten sichtbar (linke breiter, rechte 340px, dunklerer Hintergrund)
- Auf < 860px: eine Spalte

- [ ] **Schritt 4: Commit**

```bash
git add beratung.html
git commit -m "feat: add two-column main layout"
```

---

## Task 4: Bedarfs-Schnellcheck (Alpine.js)

**Files:**
- Modify: `beratung.html`

- [ ] **Schritt 1: CSS für Schnellcheck ergänzen**

Füge in `<style>` ein:

```css
/* SCHNELLCHECK */
.progress-track {
  display: flex; gap: 4px; margin-bottom: 1.5rem;
}
.prog-seg {
  flex: 1; height: 3px; border-radius: 2px;
  background: rgba(255,255,255,0.1); transition: background 0.3s;
}
.prog-seg.done { background: var(--copper); }
.prog-seg.active { background: rgba(192,120,48,0.45); }

.check-step-label {
  font-size: 0.63rem; color: var(--muted); letter-spacing: 0.08em;
  text-transform: uppercase; margin-bottom: 0.625rem;
}
.check-question {
  font-size: 0.95rem; color: rgba(255,255,255,0.85);
  line-height: 1.55; margin-bottom: 1.25rem; font-weight: 300;
}
.check-options { display: flex; flex-direction: column; gap: 0.5rem; margin-bottom: 1.5rem; }
.check-opt {
  display: flex; align-items: center; gap: 0.75rem;
  background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.1);
  border-radius: 3px; padding: 0.75rem 1rem;
  font-size: 0.82rem; color: rgba(255,255,255,0.65);
  cursor: pointer; transition: all 0.2s;
}
.check-opt:hover { border-color: var(--copper); color: white; }
.check-opt.is-selected { border-color: var(--copper); color: white; background: rgba(192,120,48,0.07); }
.check-radio {
  width: 15px; height: 15px; border-radius: 50%;
  border: 1px solid rgba(255,255,255,0.2); flex-shrink: 0; transition: all 0.2s;
}
.check-opt.is-selected .check-radio { background: var(--copper); border-color: var(--copper); }

.btn-weiter {
  background: var(--copper); color: white;
  border: none; border-radius: 2px;
  padding: 0.75rem 1.75rem; font-size: 0.875rem;
  font-weight: 500; cursor: pointer; letter-spacing: 0.03em;
  font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.btn-weiter:hover { background: var(--copper-light); transform: translateY(-1px); }
.btn-weiter:disabled { opacity: 0.4; cursor: not-allowed; transform: none; }

/* Ergebnis-Box */
.check-result {
  background: rgba(192,120,48,0.07); border: 1px solid rgba(192,120,48,0.25);
  border-radius: 4px; padding: 1.5rem;
}
.check-result-label {
  font-size: 0.63rem; color: var(--copper-light); letter-spacing: 0.12em;
  text-transform: uppercase; margin-bottom: 0.5rem;
}
.check-result-text {
  font-size: 0.9rem; color: rgba(255,255,255,0.8); line-height: 1.6; margin-bottom: 1.25rem;
}
```

- [ ] **Schritt 2: Alpine-Daten als Script-Block hinzufügen**

Füge direkt vor dem schließenden `</body>` ein:

```html
<script>
  function schnellcheck() {
    return {
      step: 0,
      selected: null,
      done: false,
      answers: [],
      questions: [
        {
          text: 'Für wen suchen Sie Versicherungsschutz?',
          opts: ['Mein Unternehmen (KMU)', 'Mich privat', 'Beides']
        },
        {
          text: 'In welchem Bereich haben Sie den größten Absicherungsbedarf?',
          opts: ['Betriebshaftpflicht & Cyber', 'Betriebliche Altersvorsorge', 'Rechtsschutz', 'Ich bin mir nicht sicher']
        },
        {
          text: 'Wie viele Mitarbeiter hat Ihr Unternehmen?',
          opts: ['1 – 10 Mitarbeiter', '11 – 50 Mitarbeiter', '51 – 100 Mitarbeiter', 'Mehr als 100']
        },
        {
          text: 'Wie dringend ist Ihr Absicherungsbedarf?',
          opts: ['Sofort — ich brauche schnell Hilfe', 'In den nächsten Wochen', 'Ich möchte mich erst informieren']
        }
      ],
      get current() { return this.questions[this.step]; },
      get resultText() {
        const a0 = this.answers[0] || '';
        const a1 = this.answers[1] || '';
        if (a0.includes('Unternehmen') || a0.includes('Beides')) {
          if (a1.includes('Cyber')) return 'Für Ihr KMU empfehlen wir eine Kombination aus Betriebshaftpflicht und Cyberversicherung. Buchen Sie jetzt einen kostenlosen Beratungstermin.';
          if (a1.includes('Altersvorsorge')) return 'Betriebliche Altersvorsorge ist steuerlich attraktiv und bindet Mitarbeiter langfristig. Wir zeigen Ihnen die besten Modelle für Ihre Unternehmensgröße.';
          if (a1.includes('Rechtsschutz')) return 'Gewerblicher Rechtsschutz schützt vor teuren Rechtsstreitigkeiten. Unser Experte berät Sie zu den passenden Tarifen.';
          return 'Basierend auf Ihren Angaben erstellen wir Ihnen eine individuelle Empfehlung. Vereinbaren Sie einen kostenlosen Termin.';
        }
        return 'Als Privatperson empfehlen wir einen umfassenden Check Ihrer bestehenden Absicherung. Unsere Experten beraten Sie unverbindlich.';
      },
      select(opt) { this.selected = opt; },
      next() {
        if (!this.selected) return;
        this.answers.push(this.selected);
        if (this.step < this.questions.length - 1) {
          this.step++;
          this.selected = null;
        } else {
          this.done = true;
        }
      }
    };
  }

  function terminbuchung() {
    return {
      selectedSlot: null,
      confirmed: false,
      slots: [
        { day: 'Mo 14.04.', time: '09:00' },
        { day: 'Mo 14.04.', time: '11:00' },
        { day: 'Di 15.04.', time: '10:00' },
        { day: 'Di 15.04.', time: '14:00' },
        { day: 'Mi 16.04.', time: '09:30' },
        { day: 'Do 17.04.', time: '15:00' },
      ],
      select(slot) { this.selectedSlot = slot; },
      confirm() {
        if (!this.selectedSlot) return;
        this.confirmed = true;
      }
    };
  }

  function chatbot() {
    return {
      input: '',
      messages: [],
      init() {
        setTimeout(() => {
          this.messages.push({ from: 'bot', text: 'Guten Tag! Ich bin Ihr digitaler Berater von Hilger Versicherungen. Wie kann ich Ihnen heute helfen?' });
        }, 600);
        setTimeout(() => {
          this.messages.push({ from: 'bot', text: 'Übrigens: Wir beraten Sie vollständig digital — ob München, Hamburg, Wien oder Zürich. 🇩🇪🇦🇹🇨🇭' });
        }, 1400);
      },
      getReply(text) {
        const t = text.toLowerCase();
        if (t.includes('cyber'))         return 'Für Cyberversicherungen sind Unternehmensgröße und IT-Infrastruktur entscheidend. Wie viele Mitarbeiter hat Ihr Unternehmen?';
        if (t.includes('haftpflicht'))   return 'Betriebshaftpflicht ist für KMU unverzichtbar. Ich empfehle unseren Schnellcheck — er dauert nur 2 Minuten.';
        if (t.includes('altersvorsorge') || t.includes('bav')) return 'Betriebliche Altersvorsorge ist steuerlich attraktiv und ein starkes Mitarbeiterbindungs-Tool. Soll ich Ihnen mehr erklären?';
        if (t.includes('termin'))        return 'Gerne! Wählen Sie einfach einen Zeitslot links aus — kostenlos und unverbindlich.';
        if (t.includes('kosten') || t.includes('preis') || t.includes('kosten')) return 'Unsere Erstberatung ist kostenlos. Die Versicherungskosten hängen von Ihrem Bedarf ab — der Schnellcheck hilft uns, ein passendes Angebot zu erstellen.';
        if (t.includes('privat'))        return 'Für Privatkunden bieten wir Haftpflicht, Krankenversicherung, Berufsunfähigkeit und Altersvorsorge. Was interessiert Sie?';
        if (t.includes('rechtsschutz'))  return 'Gewerblicher Rechtsschutz schützt vor teuren Rechtsstreitigkeiten. Wir vergleichen die besten Anbieter für Sie.';
        if (t.includes('flotte') || t.includes('auto')) return 'Flottenversicherungen für KMU sind unser Spezialgebiet. Ab wie vielen Fahrzeugen suchen Sie eine Lösung?';
        if (t.includes('dach') || t.includes('österreich') || t.includes('schweiz')) return 'Ja, wir beraten deutschlandweit und im gesamten DACH-Raum — vollständig digital, ohne Ortstermin.';
        return 'Das beantworte ich gerne im persönlichen Gespräch. Vereinbaren Sie einfach einen kostenlosen Termin über das Formular links!';
      },
      send(text) {
        const msg = (text || this.input).trim();
        if (!msg) return;
        this.messages.push({ from: 'user', text: msg });
        this.input = '';
        const self = this;
        setTimeout(() => { self.messages.push({ from: 'bot', text: self.getReply(msg) }); }, 420);
      }
    };
  }
</script>
```

- [ ] **Schritt 3: Schnellcheck-HTML in linke Spalte einfügen**

Ersetze `<!-- Schnellcheck (folgt Task 4) -->` durch:

```html
      <!-- SCHNELLCHECK -->
      <div x-data="schnellcheck()">
        <div class="s-tag">Schritt 1</div>
        <h2 class="s-title">Ihr persönlicher<br><em>Bedarfs-Schnellcheck</em></h2>

        <!-- Fortschrittsbalken -->
        <div class="progress-track">
          <template x-for="(q, i) in questions" :key="i">
            <div class="prog-seg"
              :class="{ done: i < step, active: i === step && !done }">
            </div>
          </template>
        </div>

        <!-- Fragen -->
        <div x-show="!done">
          <div class="check-step-label" x-text="'Frage ' + (step + 1) + ' von ' + questions.length"></div>
          <div class="check-question" x-text="current.text"></div>
          <div class="check-options">
            <template x-for="opt in current.opts" :key="opt">
              <div class="check-opt" :class="{ 'is-selected': selected === opt }" @click="select(opt)">
                <div class="check-radio"></div>
                <span x-text="opt"></span>
              </div>
            </template>
          </div>
          <button class="btn-weiter" @click="next()" :disabled="!selected">
            <span x-text="step < questions.length - 1 ? 'Weiter →' : 'Auswertung anzeigen →'"></span>
          </button>
        </div>

        <!-- Ergebnis -->
        <div x-show="done" x-transition>
          <div class="check-result">
            <div class="check-result-label">Ihre persönliche Empfehlung</div>
            <p class="check-result-text" x-text="resultText"></p>
            <a href="#termin" class="btn-weiter" style="display:inline-block;text-decoration:none;">Jetzt Termin buchen →</a>
          </div>
        </div>
      </div>
```

- [ ] **Schritt 4: Im Browser prüfen**

Erwartetes Ergebnis:
- Fortschrittsbalken mit 4 Segmenten sichtbar
- Frage 1 mit Optionen klickbar
- Auswahl färbt sich copper
- "Weiter" bleibt disabled bis eine Auswahl getroffen
- Nach Frage 4: Ergebnis-Box erscheint mit individualisiertem Text

- [ ] **Schritt 5: Commit**

```bash
git add beratung.html
git commit -m "feat: add bedarfs-schnellcheck with alpine.js"
```

---

## Task 5: Terminbuchung

**Files:**
- Modify: `beratung.html`

- [ ] **Schritt 1: CSS für Terminbuchung ergänzen**

Füge in `<style>` ein:

```css
/* TERMINBUCHUNG */
#termin { scroll-margin-top: 5rem; }
.slot-grid {
  display: grid; grid-template-columns: repeat(3, 1fr); gap: 0.5rem;
  margin-bottom: 1rem;
}
.slot {
  background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.1);
  border-radius: 3px; padding: 0.7rem 0.5rem; text-align: center;
  cursor: pointer; transition: all 0.2s;
}
.slot:hover { border-color: var(--copper); }
.slot.is-selected { border-color: var(--copper); background: rgba(192,120,48,0.08); }
.slot-day { font-size: 0.62rem; color: var(--muted); display: block; margin-bottom: 3px; }
.slot-time { font-size: 0.82rem; color: rgba(255,255,255,0.75); font-weight: 500; }
.slot.is-selected .slot-time { color: var(--copper-light); }

.btn-confirm {
  width: 100%; background: var(--copper); color: white;
  border: none; border-radius: 2px;
  padding: 0.875rem; font-size: 0.875rem;
  font-weight: 500; cursor: pointer; letter-spacing: 0.03em;
  font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.btn-confirm:hover { background: var(--copper-light); }
.btn-confirm:disabled { opacity: 0.4; cursor: not-allowed; }

.confirm-box {
  background: rgba(76,175,80,0.07); border: 1px solid rgba(76,175,80,0.25);
  border-radius: 4px; padding: 1.5rem; text-align: center;
}
.confirm-icon { font-size: 2rem; margin-bottom: 0.5rem; }
.confirm-title { font-family: 'Cormorant Garamond', serif; font-size: 1.4rem; color: white; margin-bottom: 0.5rem; }
.confirm-sub { font-size: 0.82rem; color: rgba(255,255,255,0.55); line-height: 1.6; }
```

- [ ] **Schritt 2: Terminbuchung-HTML einfügen**

Ersetze `<!-- Terminbuchung (folgt Task 5) -->` durch:

```html
      <div class="section-divider"></div>

      <!-- TERMINBUCHUNG -->
      <div id="termin" x-data="terminbuchung()">
        <div class="s-tag">Schritt 2</div>
        <h2 class="s-title">Termin <em>vereinbaren</em></h2>

        <div x-show="!confirmed">
          <p style="font-size:0.82rem;color:var(--muted);margin-bottom:1.25rem;line-height:1.6;">
            Wählen Sie einen kostenlosen Beratungstermin. Alle Gespräche finden per Video oder Telefon statt — deutschlandweit.
          </p>
          <div class="slot-grid">
            <template x-for="slot in slots" :key="slot.day + slot.time">
              <div class="slot"
                :class="{ 'is-selected': selectedSlot === slot }"
                @click="select(slot)">
                <span class="slot-day" x-text="slot.day"></span>
                <span class="slot-time" x-text="slot.time"></span>
              </div>
            </template>
          </div>
          <button class="btn-confirm" @click="confirm()" :disabled="!selectedSlot">
            Termin bestätigen – kostenlos &amp; unverbindlich
          </button>
        </div>

        <div x-show="confirmed" x-transition class="confirm-box">
          <div class="confirm-icon">✓</div>
          <div class="confirm-title">Termin bestätigt</div>
          <p class="confirm-sub">
            Ihr Termin am <strong x-text="selectedSlot ? selectedSlot.day + ' um ' + selectedSlot.time + ' Uhr' : ''"></strong> ist vorgemerkt.<br>
            Sie erhalten in Kürze eine Bestätigungs-E-Mail.
          </p>
        </div>
      </div>
```

- [ ] **Schritt 3: Im Browser prüfen**

Erwartetes Ergebnis:
- 6 Zeitslots im 3×2-Grid
- Slot-Klick: copper-Rahmen + Hintergrundton
- "Termin bestätigen" disabled bis Slot gewählt
- Nach Klick: grüne Bestätigungsbox mit gewähltem Termin

- [ ] **Schritt 4: Commit**

```bash
git add beratung.html
git commit -m "feat: add terminbuchung with slot selection"
```

---

## Task 6: Chatbot-Panel (Alpine.js)

**Files:**
- Modify: `beratung.html`

- [ ] **Schritt 1: CSS für Chatbot ergänzen**

Füge in `<style>` ein:

```css
/* CHATBOT */
.chat-header {
  padding: 1rem 1.25rem;
  border-bottom: 1px solid rgba(255,255,255,0.07);
  display: flex; align-items: center; gap: 0.75rem;
  flex-shrink: 0;
}
.chat-avatar {
  width: 36px; height: 36px; border-radius: 50%;
  background: linear-gradient(135deg, var(--copper), var(--copper-light));
  display: flex; align-items: center; justify-content: center;
  font-size: 0.7rem; font-weight: 700; color: white; flex-shrink: 0;
}
.chat-name { font-size: 0.82rem; font-weight: 600; color: white; }
.chat-status {
  font-size: 0.63rem; color: #4CAF50;
  display: flex; align-items: center; gap: 4px;
}
.chat-status::before {
  content: ''; display: block; width: 5px; height: 5px;
  border-radius: 50%; background: #4CAF50;
}

.chat-messages {
  flex: 1; padding: 1rem; overflow-y: auto;
  display: flex; flex-direction: column; gap: 0.75rem;
  scroll-behavior: smooth;
}
.msg-wrap { display: flex; flex-direction: column; }
.msg-wrap.user { align-items: flex-end; }
.msg-bubble {
  max-width: 88%; padding: 0.625rem 0.875rem;
  font-size: 0.78rem; line-height: 1.55; border-radius: 8px;
}
.msg-wrap.bot .msg-bubble {
  background: rgba(255,255,255,0.06); border: 1px solid rgba(255,255,255,0.09);
  border-radius: 8px 8px 8px 2px; color: rgba(255,255,255,0.8);
}
.msg-wrap.user .msg-bubble {
  background: rgba(192,120,48,0.14); border: 1px solid rgba(192,120,48,0.25);
  border-radius: 8px 8px 2px 8px; color: rgba(255,255,255,0.85);
}

.chat-suggestions {
  padding: 0 1rem 0.625rem;
  display: flex; flex-wrap: wrap; gap: 0.4rem; flex-shrink: 0;
}
.sug-btn {
  background: rgba(255,255,255,0.04); border: 1px solid rgba(255,255,255,0.1);
  border-radius: 12px; padding: 0.3rem 0.75rem;
  font-size: 0.68rem; color: rgba(255,255,255,0.55);
  cursor: pointer; font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.sug-btn:hover { border-color: var(--copper); color: var(--copper-light); }

.chat-input-row {
  padding: 0.75rem 1rem;
  border-top: 1px solid rgba(255,255,255,0.07);
  display: flex; gap: 0.5rem; align-items: center; flex-shrink: 0;
}
.chat-input {
  flex: 1; background: rgba(255,255,255,0.05);
  border: 1px solid rgba(255,255,255,0.12); border-radius: 3px;
  padding: 0.6rem 0.875rem; font-size: 0.78rem;
  color: rgba(255,255,255,0.75); font-family: 'DM Sans', sans-serif; outline: none;
  transition: border-color 0.2s;
}
.chat-input:focus { border-color: var(--copper); }
.chat-input::placeholder { color: rgba(255,255,255,0.25); }
.chat-send-btn {
  background: var(--copper); border: none; border-radius: 3px;
  width: 34px; height: 34px; cursor: pointer; flex-shrink: 0;
  display: flex; align-items: center; justify-content: center;
  color: white; font-size: 0.9rem; transition: background 0.2s;
}
.chat-send-btn:hover { background: var(--copper-light); }
```

- [ ] **Schritt 2: Chatbot-HTML einfügen**

Ersetze `<!-- Chatbot (folgt Task 6) -->` durch:

```html
      <div x-data="chatbot()" x-init="init()" style="display:contents">
        <!-- Header -->
        <div class="chat-header">
          <div class="chat-avatar">KI</div>
          <div>
            <div class="chat-name">Hilger KI-Berater</div>
            <div class="chat-status">Online · Antwortet sofort</div>
          </div>
        </div>

        <!-- Nachrichten -->
        <div class="chat-messages" x-ref="feed">
          <template x-for="(msg, i) in messages" :key="i">
            <div class="msg-wrap" :class="msg.from">
              <div class="msg-bubble" x-text="msg.text"></div>
            </div>
          </template>
        </div>

        <!-- Schnellauswahl -->
        <div class="chat-suggestions">
          <button class="sug-btn" @click="send('Betriebshaftpflicht')">Betriebshaftpflicht</button>
          <button class="sug-btn" @click="send('Altersvorsorge')">Altersvorsorge</button>
          <button class="sug-btn" @click="send('Termin buchen')">Termin buchen</button>
          <button class="sug-btn" @click="send('Kosten und Preise')">Kosten & Preise</button>
        </div>

        <!-- Eingabe -->
        <div class="chat-input-row">
          <input
            class="chat-input"
            type="text"
            placeholder="Ihre Frage..."
            x-model="input"
            @keydown.enter="send()"
          >
          <button class="chat-send-btn" @click="send()">→</button>
        </div>
      </div>
```

- [ ] **Schritt 3: Im Browser prüfen**

Erwartetes Ergebnis:
- Chatbot startet automatisch mit zwei Nachrichten (600ms + 1400ms Verzögerung)
- Schnellauswahl-Buttons senden Nachricht, Bot antwortet nach ~420ms
- Freitext-Eingabe + Enter oder → funktioniert
- Keyword-Erkennung: "cyber" → passende Antwort, "termin" → Verweis auf Slots links
- Fallback bei unbekannten Eingaben

- [ ] **Schritt 4: Commit**

```bash
git add beratung.html
git commit -m "feat: add static ai chatbot with keyword responses"
```

---

## Task 7: Nav-Link in index.html aktualisieren

**Files:**
- Modify: `index.html` (Zeilen 358, 372, 362)

- [ ] **Schritt 1: Desktop-Nav anpassen**

In `index.html` Zeile 358, ersetze:
```html
      <li><a href="#prozess">Beratung</a></li>
```
durch:
```html
      <li><a href="beratung.html">Online-Beratung</a></li>
```

- [ ] **Schritt 2: Mobile-Nav anpassen**

In `index.html` Zeile 372, ersetze:
```html
    <a href="#prozess"    @click="menuOpen = false">Beratung</a>
```
durch:
```html
    <a href="beratung.html" @click="menuOpen = false">Online-Beratung</a>
```

- [ ] **Schritt 3: CTA-Button in Nav anpassen**

In `index.html` Zeile 362, ersetze:
```html
    <a href="#kontakt" class="btn-primary nav-cta">Beratung buchen</a>
```
durch:
```html
    <a href="beratung.html" class="btn-primary nav-cta">Online-Beratung</a>
```

- [ ] **Schritt 4: Im Browser prüfen**

Öffne `index.html`. Erwartetes Ergebnis:
- Nav zeigt "Online-Beratung" statt "Beratung"
- Klick auf den Link öffnet `beratung.html`
- Zurück-Navigation in `beratung.html` ("Hilger"-Logo, "Leistungen" etc.) landet auf `index.html`

- [ ] **Schritt 5: Commit**

```bash
git add index.html
git commit -m "feat: link nav to beratung.html subpage"
```

---

## Task 8: Footer für beratung.html

**Files:**
- Modify: `beratung.html`

- [ ] **Schritt 1: CSS für Footer ergänzen**

Füge in `<style>` ein:

```css
/* FOOTER */
footer {
  background: var(--navy-mid); border-top: 1px solid rgba(255,255,255,0.07);
  padding: 2rem; text-align: center;
}
.footer-text { font-size: 0.75rem; color: var(--muted); line-height: 1.7; }
.footer-text a { color: var(--copper-light); text-decoration: none; }
.footer-text a:hover { text-decoration: underline; }
```

- [ ] **Schritt 2: Footer-HTML einfügen**

Füge direkt vor `<script>` (am Ende von `<body>`) ein:

```html
  <footer>
    <p class="footer-text">
      © 2025 Hilger Versicherungen — <a href="index.html">Startseite</a> · <a href="index.html#kontakt">Impressum</a> · <a href="index.html#kontakt">Datenschutz</a><br>
      Deutschlandweite Beratung · DACH-weit verfügbar · 100 % digital
    </p>
  </footer>
```

- [ ] **Schritt 3: Im Browser prüfen**

Erwartetes Ergebnis:
- Footer unter dem zweispaltigen Content sichtbar
- Links funktionieren

- [ ] **Schritt 4: Abschluss-Commit**

```bash
git add beratung.html
git commit -m "feat: add footer to beratung.html"
```

---

## Self-Review

**Spec-Abdeckung:**

| Spec-Anforderung | Task |
|---|---|
| Neue Subseite `beratung.html` | Task 1 |
| Nav identisch zur Hauptseite | Task 1 |
| Hero mit DACH-Botschaft | Task 2 |
| Zwei-Spalten-Layout | Task 3 |
| Responsive (< 860px eine Spalte) | Task 3 |
| Bedarfs-Schnellcheck 4 Fragen + Fortschrittsbalken | Task 4 |
| Individualisiertes Ergebnis nach Check | Task 4 |
| Terminbuchung mit Slot-Auswahl | Task 5 |
| Bestätigungsbox nach Buchung | Task 5 |
| Chatbot mit Auto-Begrüßung + DACH-Hinweis | Task 6 |
| Alle 9 Keyword-Antworten + Fallback | Task 6 |
| Schnellauswahl-Buttons im Chat | Task 6 |
| Nav-Link in index.html aktualisieren | Task 7 |
| Alpine.js state management | Tasks 4, 5, 6 |

**Placeholder-Scan:** Keine TBDs, alle Code-Blöcke vollständig.

**Typ-Konsistenz:** `schnellcheck()`, `terminbuchung()`, `chatbot()` — alle in Task 4 als Script definiert und in Tasks 4–6 verwendet. Keine Abweichungen.
