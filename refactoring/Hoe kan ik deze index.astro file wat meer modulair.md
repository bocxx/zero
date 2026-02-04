<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Hoe kan ik deze index.astro file wat meer modulair maken of werken met componenten? En is dat in deze efficient?

Je index.astro is nu eigenlijk een complete landing page in één file: data‑load bovenin, vervolgens een lange HTML‑structuur, inline scripts en een flinke `<style>`‑blok.[^1_1]

### Wat kun je goed modulair maken?

Vuistregel: elke “visuele sectie” of “logische unit” met eigen markup + styling → eigen component.

Concrete kandidaten uit je file:[^1_1]

- Layout heb je al als component (goed).
- Hero (lobster‑icon, title, tagline, description).
- LatestPost badge / chip.
- CTAGrid met de vier knoppen (Discord, Docs, GitHub, ClawHub).
- Testimonials carousel (rijen + animatielogica).
- QuickStart code‑blok met de verschillende modi + inline JS.
- Features grid.
- Integrations pills + links.
- PressSection.
- NewsletterForm.
- Footer.

Per stuk kun je een `.astro` component maken in `src/components/`:

```tsx
---
// src/components/Hero.astro
const { taglineId = "tagline" } = Astro.props;
---
<header class="hero">
  <div class="lobster-icon" aria-hidden="true">
    <!-- svg hier -->
  </div>
  <h1 class="title">
    <span class="title-main">OpenClaw</span>
  </h1>
  <p class="tagline" id={taglineId}>The AI that actually does things.</p>
  <p class="description">
    Clears your inbox, sends emails, manages your calendar, ...
  </p>
</header>
```

En in `index.astro`:

```tsx
---
import Layout from "../layouts/Layout.astro";
import Hero from "../components/Hero.astro";
import CTAGrid from "../components/CTAGrid.astro";
import Testimonials from "../components/Testimonials.astro";
import QuickStart from "../components/QuickStart.astro";
// etc.
---
<Layout title="OpenClaw – Personal AI Assistant">
  <div class="stars"></div>
  <div class="nebula"></div>

  <main class="container">
    <Hero />
    <LatestPost latestPost={latestPost} />
    <CTAGrid />
    <Testimonials testimonials={testimonials} />
    <QuickStart />
    <Features />
    <Integrations pills={integrationPills} />
    <PressSection />
    <Newsletter />
  </main>
</Layout>
```


### CSS modulair maken

Je `<style>`‑blok is nu gigantisch en alles hangt aan globale classes.[^1_1]

Twee opties die goed bij Astro passen:

1. **Section‑CSS in component‑styles**
Elk component krijgt zijn eigen `<style>` (scoped):

```tsx
<!-- Hero.astro -->
<style>
  .hero { text-align: center; margin-bottom: 56px; }
  .lobster-icon { /* ... */ }
  .title { /* ... */ }
  .tagline { /* ... */ }
</style>
```

Dan blijft alleen echt globale styling (variables, resets, body, background, `.stars`, `.nebula`) in b.v. `src/styles/global.css` die je in je layout importeert.
2. **Utility / design tokens globaal, layout‑rules in components**
Alles met `var(--coral-bright)`, `--bg-deep`, fonts, etc. zet je in een `:root` / theme file.[^1_1]
Component‑specifieke grid/spacing/hover‑rules gaan in het component.

Dit is qua performance efficiënt: Astro compileert statische CSS en co‑locatie maakt je bundel niet per se groter; het wordt alleen beter gestructureerd.

### Scripts / interactiviteit modulair

Je hebt nu inline `<script>` voor o.a. Dalek‑mode (hover over lobster) en QuickStart‑switches.[^1_1]

In Astro kun je dat beter in aparte client‑components zetten:

- Voor simpele DOM‑manipulatie: een kleine vanilla component met `client:load` of `client:idle`.
- Voor complexere state (tab‑switches, OS detection) is een kleine React/Svelte/Vue component vaak schoner.

Voorbeeld: QuickStart als React component:

```tsx
// QuickStart.tsx
import { useState } from "react";

export default function QuickStart() {
  const [mode, setMode] = useState<"oneliner"|"quick"|"hackable"|"macos">("oneliner");
  // render je tabs en code-blokken conditioneel
}
```

En in `QuickStart.astro`:

```tsx
---
import QuickStartClient from "./QuickStart.tsx";
---
<section class="quickstart">
  <h2 class="section-title"><span class="claw-accent">Quick Start</span></h2>
  <QuickStartClient client:idle />
</section>
```

Zo voorkom je lange imperatieve scripts en krijg je herbruikbare UI‑logica.

### Is dit efficiënt?

Ja, onder Astro zelfs heel efficiënt, mits je:[^1_1]

- Data‑fetch (`getCollection`, testimonials import) **in index.astro** houdt en via props doorgeeft.
- Componenten zo veel mogelijk **pure / stateless** maakt (alleen props → markup).
- Client‑side JS alleen gebruikt waar nodig (QuickStart toggles, Dalek‑hover, carrousels).
    - Gebruik `client:idle` of `client:visible` i.p.v. overal `client:load` voor minder blocking JS.
- Geen grote globale scripts in `<script>` in `index.astro` laat hangen.

Rendertijd aan serverkant blijft ongeveer gelijk (zelfde HTML), bundel kan juist kleiner en beter cachebaar worden door gerichte splitting. Voor de testimonials‑carrousel kun je die berekende `duration1/2` gewoon in een `Testimonials.astro` doen, dat verandert niks aan performance.[^1_1]

### Praktisch startplan

Als je dit stapsgewijs wil aanpakken:

1. Extract **Hero**, **CTAGrid**, **Footer** naar simpele `.astro` components zonder props.
2. Verhuis voor elke sectie de gerelateerde CSS naar het component‑style‑blok.
3. Daarna pas de complexere dingen: **Testimonials** en **QuickStart** als eigen componenten met props en evt. client‑component voor de JS‑logica.
4. Laat index.astro uiteindelijk alleen nog: data‑fetch + compositie van secties.

Als je wil, kan ik één sectie (bijv. Testimonials of QuickStart) helemaal voor je herschrijven naar losse Astro + client componenten zodat je een concreet patroon hebt om de rest te volgen.

<div align="center">⁂</div>

[^1_1]: paste.txt


---

# paste.txt

Je tweede `paste.txt` is nu een nette, losse CSS‑module (bijv. `claws.css`) met alleen de component‑styles voor Hero, Latest Post, Testimonials, Quickstart, Newsletter en Footer, losgetrokken uit je gigantische `index.astro` inline `<style>`.[^2_1]

### Hoe dit nu het beste gebruiken

- Importeer deze CSS één keer globaal (bijv. in `src/styles/claws.css`) via je `Layout.astro` of `src/pages/_app`‑achtige entry, zodat alle pagina’s dezelfde component‑classes kunnen gebruiken.[^2_1]
- Of splits hem verder op per sectie: `hero.css`, `latest-post.css`, `testimonials.css`, `quickstart.css`, `newsletter.css`, `footer.css` en importeer ze in de corresponderende `.astro` componenten; Astro scope’t niet automatisch, maar co‑locatie maakt onderhoud makkelijker.[^2_1]
- Je CSS gebruikt nu design tokens (`--space-*`, `--color-*`, `--font-*`), dus zorg dat die in een centrale `tokens.css` / `global.css` staan, en laat component‑CSS alleen de layout en animatie doen.[^2_1]


### Goed nieuws voor modulariteit/efficiëntie

- Dit is exact de richting die je wilde: je HTML/Astro‑componenten kunnen nu lean blijven en styling centraliseer je in herbruikbare modules.[^2_1]

```
- Performance‑wise is dit prima: één (of een paar) CSS‑files die via `<link rel="stylesheet">` geladen worden is net zo efficiënt of efficiënter dan één groot inline `<style>` in `index.astro`, zeker met caching over meerdere pagina’s.[^2_1]
```

Als je wil, kan ik nu op basis van deze CSS een voorbeeldcomponent `Hero.astro` + `Testimonials.astro` uitschrijven die perfect aansluit op deze class‑namen en tokens, zodat je direct ziet hoe je index kunt opsplitsen.
<span style="display:none">[^2_2]</span>

<div align="center">⁂</div>

[^2_1]: paste.txt

[^2_2]: paste.txt


---

# Ja graag schrijf dit zo efficient en best performance wise uit!

Hier is een uitgewerkte, performance‑vriendelijke opsplitsing met Astro‑components, aansluitend op je bestaande `theme.css`, `components.css` en `claws.css` setup.[^3_1][^3_2]

## 1. index.astro: alleen data + compositie

```tsx
---
import Layout from "../layouts/Layout.astro";
import Hero from "../components/Hero.astro";
import LatestPost from "../components/LatestPost.astro";
import CTAGrid from "../components/CTAGrid.astro";
import Testimonials from "../components/Testimonials.astro";
import QuickStart from "../components/QuickStart.astro";
import Features from "../components/Features.astro";
import IntegrationsPreview from "../components/IntegrationsPreview.astro";
import PressSection from "../components/PressSection.astro";
import Newsletter from "../components/Newsletter.astro";
import SiteFooter from "../components/SiteFooter.astro";

import testimonials from "../data/testimonials.json";
import { getCollection } from "astro:content";

// blog data
const posts = await getCollection("blog", ({ data }) => !data.draft);
const latestPost = posts.sort((a, b) => b.data.date.getTime() - a.data.date.getTime())[^3_0];

// integration pills (zelfde data als nu, alleen hier centraal)
import {
  siWhatsapp,
  siTelegram,
  siDiscord,
  siSignal,
  siApple,
  siAnthropic,
  siSpotify,
  siObsidian,
  siGithub,
  siGooglechrome,
  siGmail,
  siX,
  siPhilipshue,
} from "simple-icons";

import { hash as iconSlack, bot as iconGPT } from "lucide-react";

const siIcon = (icon: any) => icon.path;

const integrationPills = [
  { name: "WhatsApp", icon: siIcon(siWhatsapp), color: "#25D366" },
  { name: "Telegram", icon: siIcon(siTelegram), color: "#26A5E4" },
  { name: "Discord", icon: siIcon(siDiscord), color: "#5865F2" },
  { name: "Slack", icon: iconSlack, color: "#E01E5A" },
  { name: "Signal", icon: siIcon(siSignal), color: "#3A76F0" },
  { name: "iMessage", icon: siIcon(siApple), color: "#007AFF" },
  { name: "Claude", icon: siIcon(siAnthropic), color: "#D4A574" },
  { name: "GPT", icon: iconGPT, color: "#00A67E" },
  { name: "Spotify", icon: siIcon(siSpotify), color: "#1DB954" },
  { name: "Hue", icon: siIcon(siPhilipshue), color: "#0065D3" },
  { name: "Obsidian", icon: siIcon(siObsidian), color: "#7C3AED" },
  { name: "Twitter", icon: siIcon(siX), color: "#FFFFFF" },
  { name: "Browser", icon: siIcon(siGooglechrome), color: "#4285F4" },
  { name: "Gmail", icon: siIcon(siGmail), color: "#EA4335" },
  { name: "GitHub", icon: siIcon(siGithub), color: "#FFFFFF" },
];

// testimonials: rij‑data + duur berekenen (server‑side, geen client JS)
const pixelsPerItem = 336;
const pixelsPerSecond = 50;
const half = Math.ceil(testimonials.length / 2);
const topRow = testimonials.slice(0, half);
const bottomRow = testimonials.slice(half);
const row1 = [...topRow, ...topRow];
const row2 = [...bottomRow, ...bottomRow];
const duration1 = (row1.length * pixelsPerItem) / pixelsPerSecond;
const duration2 = (row2.length * pixelsPerItem) / pixelsPerSecond;
---

<Layout title="OpenClaw – Personal AI Assistant">
  <div class="starfield" />
  <div class="nebula" />

  <main class="container">
    <Hero />

    {latestPost && <LatestPost latestPost={latestPost} />}

    <CTAGrid />

    <Testimonials
      topRow={row1}
      bottomRow={row2}
      duration1={duration1}
      duration2={duration2}
    />

    <QuickStart />

    <Features />

    <IntegrationsPreview pills={integrationPills} />

    <PressSection />

    <Newsletter />

    <SiteFooter />
  </main>
</Layout>
```


## 2. Hero.astro (incl. Dalek‑mode, minimal JS)

```tsx
---
const taglineId = "tagline";
---

<header class="hero">
  <div class="lobster-icon" aria-hidden="true" id="lobster-icon">
    <!-- bestaande SVG 1:1 overnemen -->
    <!-- ... -->
  </div>

  <h1 class="title">
    <span class="title-main">OpenClaw</span>
  </h1>

  <p class="tagline" id={taglineId}>
    The AI that actually does things.
  </p>

  <p class="description">
    Clears your inbox, sends emails, manages your calendar, checks you in for flights.
    <br />
    All from WhatsApp, Telegram, or any chat app you already use.
  </p>
</header>

<script>
  const lobsterIcon = document.getElementById("lobster-icon");
  const tagline = document.getElementById("tagline");
  if (!lobsterIcon || !tagline) return;

  const originalTagline = tagline.textContent;
  let isAnimating = false;

  lobsterIcon.addEventListener("mouseenter", () => {
    if (isAnimating) return;
    isAnimating = true;

    tagline.style.transition = "opacity 0.2s ease";
    tagline.style.opacity = "0";

    setTimeout(() => {
      tagline.textContent = "EXFOLIATE! EXFOLIATE!";
      tagline.classList.add("dalek-mode");
      tagline.style.opacity = "1";
    }, 200);

    setTimeout(() => {
      tagline.style.opacity = "0";
      setTimeout(() => {
        tagline.textContent = originalTagline || "";
        tagline.classList.remove("dalek-mode");
        tagline.style.opacity = "1";
        isAnimating = false;
      }, 200);
    }, 2000);
  });
</script>
```

Deze JS draait server‑side niet, en client‑side alleen voor deze ene hover‑easter‑egg, dus impact is minimaal.[^3_1]

## 3. Testimonials.astro (pure server‑rendered carousel)

```tsx
---
interface Testimonial {
  url: string;
  author: string;
  quote: string;
}

interface Props {
  topRow: Testimonial[];
  bottomRow: Testimonial[];
  duration1: number;
  duration2: number;
}

const { topRow, bottomRow, duration1, duration2 } = Astro.props;
---

<section class="testimonials">
  <div class="section-header">
    <h2 class="section-title">
      <span class="claw-accent">What People Say</span>
    </h2>
    <a href="/shoutouts" class="section-link">
      View all <span class="sr-only">community shoutouts</span><span aria-hidden="true">→</span>
    </a>
  </div>

  <div class="testimonials-track">
    <div
      class="testimonials-row row-1"
      style={`--duration: ${duration1}s;`}
    >
      {topRow.map((t) => (
        <a
          href={t.url}
          target="_blank"
          rel="noopener"
          class="testimonial-card"
          tabindex="-1"
        >
          <img
            src={`https://unavatar.io/x/${t.author}`}
            alt={t.author}
            class="testimonial-avatar"
            loading="lazy"
            onerror={`this.src='https://ui-avatars.com/api/?name=${encodeURIComponent(
              t.author,
            )}&background=FF4D4D&color=fff&size=88'`}
          />
          <div class="testimonial-content">
            <p class="testimonial-quote">{t.quote}</p>
            <span class="testimonial-author">{t.author}</span>
          </div>
        </a>
      ))}
    </div>

    <div
      class="testimonials-row row-2"
      style={`--duration: ${duration2}s;`}
    >
      {bottomRow.map((t) => (
        <a
          href={t.url}
          target="_blank"
          rel="noopener"
          class="testimonial-card"
        >
          <img
            src={`https://unavatar.io/x/${t.author}`}
            alt={t.author}
            class="testimonial-avatar"
            loading="lazy"
            onerror={`this.src='https://ui-avatars.com/api/?name=${encodeURIComponent(
              t.author,
            )}&background=FF4D4D&color=fff&size=88'`}
          />
          <div class="testimonial-content">
            <p class="testimonial-quote">{t.quote}</p>
            <span class="testimonial-author">{t.author}</span>
          </div>
        </a>
      ))}
    </div>
  </div>
</section>
```

De animatie (scroll‑left/right) gebeurt volledig via CSS met `--duration`, dus nul extra JS.[^3_2][^3_1]

## 4. QuickStart.astro (lightweight, zonder framework)

Dit houdt alle logica in één klein script, maar de markup blijft SSR‑vriendelijk; de default staat direct goed zonder JS.[^3_1]

```tsx
---
---

<section class="quickstart">
  <div class="section-header">
    <h2 class="section-title">
      <span class="claw-accent">Quick Start</span>
    </h2>
    <a href="https://docs.openclaw.ai/getting-started" target="_blank" rel="noopener" class="section-link">
      Open docs<span aria-hidden="true">↗</span>
    </a>
  </div>

  <div class="code-block" data-mode="oneliner" data-pm="npm" data-os="unix" data-shell="powershell" data-beta="false">
    <div class="code-header">
      <span class="code-dot"></span>
      <span class="code-dot"></span>
      <span class="code-dot"></span>

      <div class="mode-switch">
        <button class="mode-btn active" data-mode="oneliner">One‑liner</button>
        <button class="mode-btn" data-mode="quick">npm</button>
        <button class="mode-btn" data-mode="hackable">Hackable</button>
        <button class="mode-btn" data-mode="macos">macOS</button>
      </div>

      <div class="pm-switch" id="pm-switch" style="display:none">
        <button class="pm-btn active" data-pm="npm">npm</button>
        <button class="pm-btn" data-pm="pnpm">pnpm</button>
      </div>

      <div class="hackable-switch" id="hackable-switch" style="display:none">
        <button class="hackable-btn active" data-hackable="installer">installer</button>
        <button class="hackable-btn" data-hackable="pnpm">pnpm</button>
      </div>

      <div class="os-indicator" id="os-indicator">
        <span class="os-detected" id="os-detected">macOS/Linux</span>
        <button class="os-change-btn" id="os-change-btn">change</button>
      </div>

      <div class="os-switch" id="os-switch" style="display:none">
        <button class="os-btn active" data-os="unix">macOS/Linux</button>
        <button class="os-btn" data-os="windows">Windows</button>
      </div>

      <div class="win-shell-switch" id="win-shell-switch" style="display:none">
        <button class="win-shell-btn active" data-shell="powershell">PowerShell</button>
        <button class="win-shell-btn" data-shell="cmd">CMD</button>
      </div>

      <div class="beta-switch" id="beta-switch">
        <button class="beta-btn" id="beta-btn" data-beta="false">
          <span class="beta-label">β</span>
          <span class="beta-text">Beta</span>
        </button>
      </div>

      <div class="switch-placeholder" id="switch-placeholder" aria-hidden="true"></div>
    </div>

    <!-- oneliner -->
    <div id="code-oneliner" class="code-content">
      <div class="code-line comment" id="oneliner-comment">
        # One command to install everything
      </div>
      <div class="code-line cmd">
        <span class="code-prompt">$</span>
        <span class="os-cmd">
          curl -fsSL https://openclaw.ai/install.sh | bash
        </span>
        <button
          class="copy-line-btn"
          data-cmd="curl -fsSL https://openclaw.ai/install.sh | bash"
          title="Copy"
        >
          <!-- icons -->
        </button>
      </div>
    </div>

    <!-- quick: npm / pnpm -->
    <div id="code-quick" class="code-content" style="display:none">
      <!-- zelfde inhoud als nu, maar opgesplitst in blokjes, geen wijziging nodig -->
    </div>

    <!-- hackable -->
    <div id="code-hackable" class="code-content" style="display:none">
      <!-- bestaande hackable installer/pnpm commands -->
    </div>

    <!-- macOS app -->
    <div id="code-macos" class="code-content" style="display:none">
      <!-- bestaande macOS download CTA -->
    </div>
  </div>

  <p class="quickstart-note">
    Works on macOS, Windows &amp; Linux. The one‑liner installs Node.js and everything else for you.
  </p>
</section>

<script>
  const root = document.querySelector(".code-block");
  if (!root) return;

  const setMode = (mode) => {
    root.dataset.mode = mode;
    root.querySelectorAll(".mode-btn").forEach((btn) => {
      btn.classList.toggle("active", btn.dataset.mode === mode);
    });

    root.querySelector("#code-oneliner").style.display = mode === "oneliner" ? "" : "none";
    root.querySelector("#code-quick").style.display = mode === "quick" ? "" : "none";
    root.querySelector("#code-hackable").style.display = mode === "hackable" ? "" : "none";
    root.querySelector("#code-macos").style.display = mode === "macos" ? "" : "none";

    root.querySelector("#pm-switch").style.display = mode === "quick" ? "flex" : "none";
    root.querySelector("#hackable-switch").style.display = mode === "hackable" ? "flex" : "none";
    root.querySelector("#beta-switch").style.display = mode === "macos" ? "flex" : "none";
  };

  root.querySelectorAll(".mode-btn").forEach((btn) => {
    btn.addEventListener("click", () => setMode(btn.dataset.mode));
  });

  // OS detection (lichtgewicht)
  const osDetected = root.querySelector("#os-detected");
  const osSwitch = root.querySelector("#os-switch");
  const osChangeBtn = root.querySelector("#os-change-btn");
  const winShellSwitch = root.querySelector("#win-shell-switch");

  const isWindows = navigator.userAgent.includes("Windows");
  root.dataset.os = isWindows ? "windows" : "unix";
  osDetected.textContent = isWindows ? "Windows" : "macOS/Linux";
  winShellSwitch.style.display = isWindows ? "flex" : "none";

  osChangeBtn.addEventListener("click", () => {
    const show = osSwitch.style.display === "none" || osSwitch.style.display === "";
    osSwitch.style.display = show ? "flex" : "none";
  });

  root.querySelectorAll(".os-btn").forEach((btn) => {
    btn.addEventListener("click", () => {
      root.dataset.os = btn.dataset.os;
      osDetected.textContent = btn.dataset.os === "windows" ? "Windows" : "macOS/Linux";
      osSwitch.style.display = "none";
      winShellSwitch.style.display = btn.dataset.os === "windows" ? "flex" : "none";

      root.querySelectorAll(".os-btn").forEach((b) => {
        b.classList.toggle("active", b === btn);
      });
    });
  });

  // pm / hackable / beta toggles + copy buttons kun je 1:1 uit je huidige script meenemen
</script>
```

Dit is minder JS dan je eerdere inline script, zonder framework‑runtime, en alles is “progressive enhancement”: zonder JS zie je nog steeds de oneliner.[^3_2][^3_1]

## 5. Kleine sectie‑components (schematisch, 1:1 met huidige markup)

Je kunt de overige secties heel dun houden:

**LatestPost.astro**

```tsx
---
interface Props {
  latestPost: {
    slug: string;
    data: { title: string };
  };
}
const { latestPost } = Astro.props;
---

<section class="latest-post">
  <a href={`/blog/${latestPost.slug}`} class="latest-post-card">
    <span class="latest-post-badge">New</span>
    <span class="latest-post-title">{latestPost.data.title}</span>
    <span class="latest-post-link">Read post →</span>
  </a>
</section>
```

**CTAGrid.astro**

```tsx
---
---

<nav class="cta-grid">
  <!-- Discord, Docs, GitHub, ClawHub a-tags 1:1 uit je huidige markup -->
</nav>
```

**Features.astro, IntegrationsPreview.astro, PressSection.astro, Newsletter.astro, SiteFooter.astro**

Die kun je vrijwel rechtstreeks copy‑paste’n uit je huidige `index.astro` in aparte files, alleen de `<style>` laat je achterwege omdat je alles al in `claws.css` hebt zitten.[^3_2][^3_1]

***

Als je wilt, kan ik per sectie het exacte Astro‑component nog uittypen (Features/Integrations/Press/Newsletter/Footer), maar dit patroon is al maximaal “lean”: data‑fetch alleen op pagina‑niveau, alle secties pure server components, en alleen daar JS waar echt nodig, zonder framework‑runtime.

<div align="center">⁂</div>

[^3_1]: paste.txt

[^3_2]: paste.txt


---

# Ja graag dit nog doen:

Als je wilt, kan ik per sectie het exacte Astro‑component nog uittypen (Features/Integrations/Press/Newsletter/Footer),

Hier zijn de ontbrekende secties als losse Astro‑components, 1:1 met je huidige markup en gebruikmakend van je bestaande CSS‑classes.[^4_1][^4_2]

## Features.astro

```tsx
---
import Icon from "../components/Icon.astro";
---

<section class="features">
  <h2 class="section-title">
    <span class="claw-accent">What It Does</span>
  </h2>

  <div class="features-grid">
    <a
      href="https://docs.openclaw.ai/getting-started"
      target="_blank"
      rel="noopener"
      class="feature-card"
    >
      <div class="feature-icon">
        <Icon icon="lucide:home" color="var(--color-primary-bright)" size={28} />
      </div>
      <h3 class="feature-title">Runs on Your Machine</h3>
      <p class="feature-desc">
        Mac, Windows, or Linux. Anthropic, OpenAI, or local models. Private by default—your data stays yours.
      </p>
    </a>

    <a href="/integrations" class="feature-card">
      <div class="feature-icon">
        <Icon icon="lucide:message-circle" color="var(--color-primary-bright)" size={28} />
      </div>
      <h3 class="feature-title">Any Chat App</h3>
      <p class="feature-desc">
        Talk to it on WhatsApp, Telegram, Discord, Slack, Signal, or iMessage. Works in DMs and group chats.
      </p>
    </a>

    <a
      href="https://docs.openclaw.ai/session"
      target="_blank"
      rel="noopener"
      class="feature-card"
    >
      <div class="feature-icon">
        <Icon icon="lucide:brain" color="var(--color-primary-bright)" size={28} />
      </div>
      <h3 class="feature-title">Persistent Memory</h3>
      <p class="feature-desc">
        Remembers you and becomes uniquely yours. Your preferences, your context, your AI.
      </p>
    </a>

    <a
      href="https://docs.openclaw.ai/browser"
      target="_blank"
      rel="noopener"
      class="feature-card"
    >
      <div class="feature-icon">
        <Icon icon="lucide:globe" color="var(--color-primary-bright)" size={28} />
      </div>
      <h3 class="feature-title">Browser Control</h3>
      <p class="feature-desc">
        It can browse the web, fill forms, and extract data from any site.
      </p>
    </a>

    <a
      href="https://docs.openclaw.ai/bash"
      target="_blank"
      rel="noopener"
      class="feature-card"
    >
      <div class="feature-icon">
        <Icon icon="lucide:terminal" color="var(--color-primary-bright)" size={28} />
      </div>
      <h3 class="feature-title">Full System Access</h3>
      <p class="feature-desc">
        Read and write files, run shell commands, execute scripts. Full access or sandboxed—your choice.
      </p>
    </a>

    <a
      href="https://docs.openclaw.ai/skills"
      target="_blank"
      rel="noopener"
      class="feature-card"
    >
      <div class="feature-icon">
        <Icon icon="lucide:puzzle" color="var(--color-primary-bright)" size={28} />
      </div>
      <h3 class="feature-title">Skills Plugins</h3>
      <p class="feature-desc">
        Extend with community skills or build your own. It can even write its own.
      </p>
    </a>
  </div>
</section>
```


## IntegrationsPreview.astro

```tsx
---
import Icon from "../components/Icon.astro";

interface Pill {
  name: string;
  icon: string; // path of simple-icons of lucide id
  color: string;
}

interface Props {
  pills: Pill[];
}

const { pills } = Astro.props;
---

<section class="integrations-preview">
  <h2 class="section-title">
    <span class="claw-accent">Works With Everything</span>
  </h2>

  <div class="integrations-row">
    {pills.map((p) => (
      <span class="integration-pill">
        <Icon icon={p.icon} color={p.color} size={16} />
        <span>{p.name}</span>
      </span>
    ))}
  </div>

  <div class="integrations-links">
    <a href="/integrations" class="integrations-link">
      View all 50 integrations
    </a>
    <span class="link-separator">•</span>
    <a href="/showcase" class="integrations-link">
      See what people built
    </a>
  </div>
</section>
```

(Als je `Icon.astro` nu al een andere API heeft, enkel de `icon`/`color`/`size` props even matchen.)[^4_1]

## PressSection.astro

```tsx
---
---

<section class="press-section">
  <h2 class="section-title">
    <span class="claw-accent">Featured In</span>
  </h2>

  <div class="press-grid">
    <a
      href="https://www.macstories.net/stories/clawdbot-showed-me-what-the-future-of-personal-ai-assistants-looks-like"
      target="_blank"
      rel="noopener"
      class="press-card press-featured"
    >
      <div class="press-logo">
        <svg viewBox="0 0 24 24" fill="currentColor" class="press-icon" aria-hidden="true">
          <path
            d="M18.71 19.5c-.83 1.24-1.71 2.45-3.05 2.47-1.34.03-1.77-.79-3.29-.79-1.53 0-2 .77-3.27.82-1.31.05-2.3-1.32-3.14-2.53C4.25 17 2.94 12.45 4.7 9.39c.87-1.52 2.43-2.48 4.12-2.51 1.28-.02 2.5.87 3.29.87.78 0 2.26-1.07 3.81-.91.65.03 2.47.26 3.64 1.98-.09.06-2.17 1.28-2.15 3.81.03 3.02 2.65 4.03 2.68 4.04-.03.07-.42 1.44-1.38 2.83M13 3.5c.73-.83 1.94-1.46 2.94-1.5.13 1.17-.34 2.35-1.04 3.19-.69.85-1.83 1.51-2.95 1.42-.15-1.15.41-2.35 1.05-3.11z"
          />
        </svg>
        <span class="press-name">MacStories</span>
      </div>
      <blockquote class="press-quote">
        OpenClaw Showed Me What the Future of Personal AI Assistants Looks Like
      </blockquote>
      <span class="press-author">Federico Viticci</span>
    </a>

    <a
      href="https://www.starryhope.com/mini-pcs/clawdbot-mac-mini-ai-agent-trend"
      target="_blank"
      rel="noopener"
      class="press-card"
    >
      <div class="press-logo">
        <svg viewBox="0 0 24 24" fill="currentColor" class="press-icon" aria-hidden="true">
          <polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2" />
        </svg>
        <span class="press-name">StarryHope</span>
      </div>
      <blockquote class="press-quote">
        The Lobster Takeover – Why Developers Are Buying Mac Minis to Run Their Own AI Agents
      </blockquote>
      <span class="press-author">Jim Mendenhall</span>
    </a>
  </div>
</section>
```


## Newsletter.astro

```tsx
---
---

<section class="newsletter">
  <div class="newsletter-content">
    <h2 class="newsletter-title">
      <span class="claw-accent">Stay in the Loop</span>
    </h2>
    <p class="newsletter-desc">
      Get updates on new features, integrations, and lobster wisdom. No spam, unsubscribe anytime.
    </p>

    <form
      class="newsletter-form"
      action="https://buttondown.com/api/emails/embed-subscribe/steipete"
      method="post"
      target="_blank"
    >
      <input type="hidden" name="tag" value="clawdbot" />
      <input
        type="email"
        name="email"
        placeholder="your@email.com"
        required
        class="newsletter-input"
        aria-label="Email address"
      />
      <button type="submit" class="newsletter-btn">
        Subscribe
        <svg
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
          aria-hidden="true"
        >
          <line x1="15" y1="12" x2="21" y2="12" />
          <polyline points="12 5 19 12 12 19" />
        </svg>
      </button>
    </form>
  </div>
</section>
```


## SiteFooter.astro

```tsx
---
---

<footer class="footer">
  <nav class="footer-nav">
    <a href="/blog">Blog</a>
    <span class="footer-separator">•</span>
    <a href="/showcase">Showcase</a>
    <span class="footer-separator">•</span>
    <a href="/shoutouts">Shoutouts</a>
    <span class="footer-separator">•</span>
    <a href="/integrations">Integrations</a>
  </nav>

  <p>
    Built by
    <a href="https://molty.me" target="_blank" rel="noopener">Molty</a>
    , a space lobster AI with a
    <a href="https://soul.md" target="_blank" rel="noopener">soul</a>
    , by
    <a href="https://steipete.me" target="_blank" rel="noopener">Peter Steinberger</a>
    &
    <a
      href="https://github.com/openclaw/openclaw#community"
      target="_blank"
      rel="noopener"
    >
      community
    </a>
    .
  </p>

  <p class="disclaimer">
    Formerly known as Clawdbot and Moltbot. Independent project, not affiliated with Anthropic.
  </p>
</footer>
```

Alles hierboven gebruikt exact je bestaande class‑namen, waardoor `theme.css`, `components.css` en `claws.css` direct blijven werken en je runtime‑performance identiek of beter is (meer cachebare CSS, minder inline style).[^4_2][^4_1]

<div align="center">⁂</div>

[^4_1]: paste.txt

[^4_2]: paste.txt

