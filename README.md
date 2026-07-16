# NMCT Website — Homepage Design Comps

Interactive homepage design comps for the **Native Medicare Charitable Trust
(NMCT)** — a Coimbatore-based charitable trust registered under the Indian
Trust Act on 13 September 1988 (Reg. No. 448/1988), working across healthcare,
education, livelihood, women's empowerment, environment, and community
programmes in Tamil Nadu.

This repository holds two competing homepage directions so stakeholders can
compare and choose before full site build-out. Both are high-fidelity, working
prototypes — not static images.

## Repository layout

```
NCMT demo/
├── demo 1/                     # Direction A — cinematic, motion-led
│   ├── NMCT Homepage.dc.html   # the page
│   ├── support.js              # DesignCraft (x-dc) React runtime (generated)
│   ├── image-slot.js           # drop-in fillable image placeholder component
│   └── uploads/                # pasted reference images
├── demo 2/                     # Direction B — structured, product-led
│   ├── NMCT Home.dc.html       # the page
│   ├── support.js              # (identical runtime to demo 1)
│   ├── image-slot.js
│   └── uploads/                # proposal & UX-recommendation PDFs
```

## How the demos are built

Both pages use the **DesignCraft `x-dc` runtime** (`support.js`) — a small
React-based framework that renders the `<x-dc>` markup in the HTML. You author
plain HTML with a few custom tags and inline styles; the runtime turns it into
a live React component.

Key pieces:

- **`support.js`** — the generated runtime. Parses the `<x-dc>` document,
  wires up the `<script type="text/x-dc">` logic block (state, event handlers,
  data), and mounts it. Do not hand-edit; it's compiled from a TypeScript
  source (`dc-runtime`).
- **`image-slot.js`** — an `<image-slot>` / `<x-import ... id="…">` placeholder.
  Every slot has a unique `id`; you can drag-drop an image onto it and the
  choice persists via an `.image-slots.state.json` sidecar next to the HTML.
  Slots are prefilled with photos from the live nmctngo.org site as stand-ins.
- **Custom template tags** (demo 2) — `<sc-for>` (list rendering),
  `<sc-if>` (conditionals), and `{{ … }}` bindings, driven by the component
  `state` / `renderVals()` defined in the trailing `<script type="text/x-dc">`.

### Viewing a demo

Open the `.dc.html` file through the DesignCraft/omelette preview host (the
runtime expects `window.React`/`window.ReactDOM` and the sidecar fetch to be
provided by that host). Opening the raw file directly from disk will not fully
render, because the runtime and image-slot persistence depend on the host
environment.

## The two directions

| | **Demo 1 — "Direction A"** | **Demo 2 — "Direction B"** |
|---|---|---|
| **Feel** | Cinematic, editorial, motion-led | Clean, structured, product-led |
| **Fonts** | Manrope + Source Serif 4 (italic pull-quotes) | Archivo + Source Sans 3 |
| **Hero** | Full-viewport parallax image, drifting particles, animated scroll cue | 620px auto-rotating slider (3 slides) with dot controls |
| **Signature section** | Horizontal scroll-jacked "story panels" (6 programme areas) + animated count-up stats + 1988→today timeline | 7-card programme grid + 3-card featured projects + impact story |
| **Interactivity** | Scroll-driven reveals & transforms | Real state: dropdown menus, hero autoplay, **live donation widget** (amount/frequency picker) |
| **Completeness** | Homepage narrative, top-of-funnel; footer not in the excerpt reviewed | Full page **including footer**, contact block, news grid, partners |
| **Best for** | Emotional first impression, storytelling, campaign landing | Everyday homepage that has to route users and drive donations |
| **Complexity to maintain** | Higher (bespoke animations, scroll math) | Lower (data-driven lists, one nav definition feeds header + footer) |

### Notable strengths

**Demo 1**
- Strong emotional hook; the horizontal story panels are memorable.
- Accessibility-aware: honours `prefers-reduced-motion`, uses `aria-label`s,
  visible focus outlines on CTAs.
- Semantic-ish structure with `data-screen-label` section markers.

**Demo 2**
- Single source of truth for navigation (`Component.NAV`) renders both the
  header dropdowns and the footer columns — easy to keep in sync.
- Functional donation module (give once / monthly, preset amounts) that mirrors
  the real donation flow.
- Ships a complete page: utility bar, partners, news & media, get-involved CTA,
  and a full footer with 12A/80G/FCRA registration placeholders.

## Before launch — content still to be supplied by NMCT

Both comps use **placeholder data** that must be replaced with verified figures
and assets:

- **Impact numbers** — Demo 1 hard-codes indicative counts (250,000+ lives,
  etc.) with a "figures are placeholders" note; Demo 2 uses bracketed
  `[XX,XXX]+` stand-ins. Replace with NMCT's audited data.
- **Registration numbers** — 12A / 80G / FCRA numbers are `[registration no.]`
  placeholders in the Demo 2 footer.
- **Photography** — image slots are prefilled from nmctngo.org; swap in
  final, licensed photography.
- **Partner logos** — currently rendered as wordmarks; replace with supplied
  logo files.
- **Real destination URLs** — donation, programme, and article links.

Reference material for content and design intent lives in
`NCMT demo/demo 2/uploads/` (the Quantzi web-development proposal and the
UI/UX recommendations PDF).

## Recommendation

For NMCT's primary homepage, **Demo 2 is the stronger production starting
point** — it is more complete, more maintainable, and directly supports the
donation and navigation jobs a nonprofit homepage must do. The most compelling
ideas from **Demo 1** (the parallax hero, the horizontal programme-story
scroll, and the animated impact counters) are worth grafting in as feature
sections to keep the emotional impact without taking on the full bespoke-motion
maintenance cost across every page.

---

*Prepared as living documentation for the NMCT homepage comps. Update as the
demos evolve.*
