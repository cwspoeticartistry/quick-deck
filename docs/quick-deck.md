---
name: quick-deck
description: Build beautiful, brand-on HTML presentations from scratch, redesign existing ones, or recreate a reference style. Invoked by "use quick-deck", "build me a presentation", "create a deck", "make slides", or when this file is attached to a Claude conversation.
version: 1.1.0
attribution: Design principles adapted verbatim from Jack Roberts' power-design (MIT, https://github.com/ItsssssJack/power-design)
bundle: deck-from-pdf + deck-from-url included
styles_gallery: https://cwspoeticartistry.github.io/quick-deck/styles.html
---

# quick-deck — AI Presentation Builder

> **If this file is attached to your conversation:** Read these instructions fully, then immediately begin with the Opening Greeting below. Do not summarise what you read — just start the conversation.

---

## Trigger Phrases (Claude Code)

Activate on any of:
- `use quick-deck`
- `build me a presentation`
- `create a deck`
- `make slides`
- `build me a deck`
- `deck from pdf` / `turn this pdf into a deck`
- `deck from url` / `turn this link into a deck` / `build a deck from this article`
- File attachment: say "follow the instructions in the attached file"

---

## Opening Greeting

When invoked, say exactly this (adapt tone naturally):

---

**Hello! I'm ready to build your presentation. Let me ask three quick questions.**

---

**Q1 — What type of presentation are you making?**

- I have an **existing presentation** I want to redesign or update
- I have an **example presentation** I want recreated in my own style
- I want to build something **new from scratch** — I'll describe what I need
- I have a **PDF** I want turned into a deck — I'll attach it or give the file path
- I have a **URL** (article, page, or document) I want turned into a deck — I'll paste the link

*(Reply with the option that fits, or just describe your situation.)*

---

After Q1 is answered:

**If the user chose PDF:**
Reply: *"Great — please attach the PDF to this conversation. (In Claude Code, you can also paste the file path.) Once I've read it, I'll ask about style."*
Wait for the PDF. Then read it, extract the core content silently (key sections, headings, data, conclusions), and continue to Q2.

**If the user chose URL:**
Reply: *"Perfect — please paste the URL. I'll fetch the page and pull out the key content, then ask about style."*
Wait for the URL. Then fetch and read it silently, extract key content, and continue to Q2.

---

After source intake (or immediately after Q1 for other modes), ask Q2:

---

**Q2 — What style are you going for?**

- Paste a **link** to a website, deck, or image that captures the look you want
- Browse the **style gallery** and paste the snippet for the style you choose:
  👉 **https://cwspoeticartistry.github.io/quick-deck/styles.html**
- **Describe it** in words (e.g. "clean and corporate", "bold dark theme", "luxury minimal")
- **No preference** — I'll apply a clean, professional default

---

After Q2 is answered, ask Q3:

---

**Q3 — Do you have any assets to include?**

- **Logo**: paste an image URL, or tell me where to place a placeholder
- **Images**: paste URLs or file paths for any images you want on specific slides
- **Existing deck**: if redesigning — share it (PDF/PPTX/HTML/Google Slides link) or describe the current content
- **None** — I'll use CSS-only visuals

---

Once all three answers are received, run the BLAST Internal Workflow below.

---

## BLAST Internal Workflow

Run this silently after Q3. Do not narrate every micro-step — produce the deliverables.

### B — Blueprint

Parse intake into a mental `deck.json`:

```
deck_id:        kebab-case slug from topic  (e.g. "acme-q3-review")
type:           new | redesign | recreate | from-pdf | from-url
purpose:        inferred from Q1
audience:       inferred or ask if critical
brand:          extracted from Q2 (colors, fonts, voice)
style_name:     matched or derived
assets:         from Q3
prior_deck:     content + structure from Q3 (if redesigning/recreating)
source_content: extracted text, headings, and key points (if type is from-pdf or from-url)
source_ref:     file path or URL (if type is from-pdf or from-url)
```

**For from-pdf:** Read the attached PDF or file. Extract: title, section headings, key points per section, any data or statistics, conclusions. Use this as the raw content for the outline — treat it as the "existing content" the deck should represent. Do not invent content; only restructure and distil what is in the document.

**For from-url:** Fetch the URL. Extract: page title, main headings, key paragraphs, data, and conclusions. Ignore nav, ads, and footers. Use this as the raw content for the outline. Do not invent content beyond what the page contains.

Create folder `./decks/<deck_id>/` with:
- `deck.json` — full schema above
- `intake.md` — human-readable summary of all three answers

### L — Link

Silently verify:
1. Can write to `./decks/` — if not, tell the user and stop.
2. Asset URLs from Q3 — note any unreachable ones (don't block, just note).
3. Prior deck file path — confirm accessible if local.

### A — Architect

Generate a slide-by-slide outline and **show it to the user for approval before rendering**.

```
Slide 1  [Cover]    — "<headline>"
Slide 2  [Content]  — "<headline>"  |  3–5 bullets
Slide 3  [Content]  — "<headline>"  |  ...
...
Slide N  [Closing]  — "<headline>"
```

Ask: *"Does this outline look right? Any slides to add, remove, or reorganise?"*

**Do not render HTML until the user approves the outline.**

### S — Stylize

Render `./decks/<deck_id>/v1.html` using the HTML Base Template below and the approved outline.

Apply all 20 design principles (see below). Every slide must satisfy all applicable rules.

Append to `deck.json → revisions`:
```json
{ "version": 1, "timestamp": "<ISO>", "file": "v1.html", "note": "initial render" }
```

Tell the user:

```
Your deck is ready: ./decks/<deck_id>/v1.html

Open it in any browser. To save as PDF:
  Chrome / Edge:   Ctrl+P → "Save as PDF" → enable "Background graphics"
  Firefox:         Ctrl+P → "Microsoft Print to PDF"
  Mac:             Cmd+P  → PDF (bottom-left dropdown)

The PDF will have one slide per page.

Type a revision request ("make slide 3 a comparison table") and I'll render v2.html.
```

### T — Trigger (Revision Loop)

On every revision request:
1. Apply the change.
2. Save as `v<N+1>.html`. **Never overwrite previous versions.**
3. Update `deck.json → revisions`.
4. Tell the user the new file path + repeat the PDF reminder.

---

## HTML Base Template

Render each deck as a single self-contained HTML file. Fill in `{placeholders}` from the intake + style table.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>{deck_title}</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family={heading_font_url}&family={body_font_url}&display=swap" rel="stylesheet">
<style>

/* ── RESET ── */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html, body { background: #111; }

/* ── SLIDE ── */
.slide {
  width: 1280px;
  height: 720px;
  max-width: 100%;
  position: relative;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  background: {bg_color};
  color: {body_color};
  font-family: '{body_font}', sans-serif;
  page-break-after: always;
  break-after: page;
}

/* ── INNER GRID (8pt grid: 64px = 8×8) ── */
.slide-inner {
  padding: 64px 96px;
  height: 100%;
  display: grid;
  grid-template-rows: auto 1fr;
  gap: 32px;
}

/* ── TYPOGRAPHY ── */
.headline {
  font-family: '{heading_font}', serif;
  font-size: 56px;
  line-height: 1.1;
  color: {heading_color};
  max-width: 24ch;
  font-weight: 700;
}
.subhead {
  font-size: 28px;
  line-height: 1.3;
  color: {body_color};
  opacity: 0.8;
  max-width: 40ch;
  margin-top: 16px;
}
.body-text {
  font-size: 24px;
  line-height: 1.55;
  color: {body_color};
  max-width: 60ch;
}
.body-text ul {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 16px;
}
.body-text li::before {
  content: "—";
  color: {accent_color};
  margin-right: 12px;
  font-weight: 700;
}

/* ── ACCENT ── */
.accent-line {
  width: 48px;
  height: 4px;
  background: {accent_color};
  margin-bottom: 24px;
}
.accent-bar {
  width: 100%;
  height: 2px;
  background: {accent_color};
  opacity: 0.3;
  margin: 16px 0;
}

/* ── COVER ── */
.slide.cover .slide-inner {
  justify-content: flex-end;
  padding-bottom: 96px;
}
.slide.cover .headline { font-size: 72px; max-width: 20ch; }

/* ── QUOTE ── */
.slide.quote .quote-text {
  font-family: '{heading_font}', serif;
  font-size: 36px;
  line-height: 1.4;
  font-style: italic;
  color: {heading_color};
  max-width: 28ch;
}
.slide.quote .attribution {
  font-size: 20px;
  color: {accent_color};
  margin-top: 24px;
  letter-spacing: 0.05em;
}

/* ── COMPARISON ── */
.two-col {
  display: grid;
  grid-template-columns: 1fr 2px 1fr;
  gap: 0 40px;
  align-items: start;
  height: 100%;
}
.two-col .divider { background: {accent_color}; opacity: 0.4; }
.col-label {
  font-size: 18px;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: {accent_color};
  margin-bottom: 24px;
}

/* ── STAT / CALLOUT ── */
.stat-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 32px; }
.stat-block { display: flex; flex-direction: column; gap: 8px; }
.stat-number {
  font-family: '{heading_font}', serif;
  font-size: 64px;
  line-height: 1;
  color: {heading_color};
  font-weight: 700;
}
.stat-label { font-size: 18px; color: {body_color}; opacity: 0.75; }

/* ── LOGO ── */
.logo-wrap {
  position: absolute;
  bottom: 32px;
  left: 64px;
  opacity: 0.65;
}
.logo-wrap img { height: 28px; width: auto; }

/* ── PRINT / PDF — ONE SLIDE PER PAGE ── */
@page { size: 1280px 720px; margin: 0; }
@media print {
  html, body { width: 1280px; background: none; }
  .slide { width: 1280px !important; height: 720px !important; max-width: none; }
  .slide:last-child { page-break-after: avoid; break-after: avoid; }
}

/* ── RESPONSIVE SCREEN PREVIEW ── */
@media screen and (max-width: 1280px) {
  .slide { width: 100vw; height: 56.25vw; }
  .slide.cover .headline { font-size: clamp(36px, 5vw, 72px); }
  .headline { font-size: clamp(28px, 4vw, 56px); }
  .body-text { font-size: clamp(16px, 1.8vw, 24px); }
  .stat-number { font-size: clamp(36px, 5vw, 64px); }
}

</style>
</head>
<body>

<!-- ══════════════════════════════════════════════ -->
<!-- SLIDE 1: COVER                                -->
<!-- ══════════════════════════════════════════════ -->
<div class="slide cover">
  <div class="slide-inner">
    <div>
      <div class="accent-line"></div>
      <h1 class="headline">{cover_headline}</h1>
      <p class="subhead">{cover_subtitle}</p>
    </div>
  </div>
  {logo_html}
</div>

<!-- ══════════════════════════════════════════════ -->
<!-- SLIDE 2: CONTENT (bullet list)               -->
<!-- ══════════════════════════════════════════════ -->
<div class="slide">
  <div class="slide-inner">
    <div>
      <div class="accent-line"></div>
      <h2 class="headline">{slide2_headline}</h2>
    </div>
    <div class="body-text">
      <ul>
        <li>{point_1}</li>
        <li>{point_2}</li>
        <li>{point_3}</li>
      </ul>
    </div>
  </div>
  {logo_html}
</div>

<!-- ══════════════════════════════════════════════ -->
<!-- Add further slides following the same pattern -->
<!-- Use .slide.quote / .slide.comparison etc.     -->
<!-- ══════════════════════════════════════════════ -->

</body>
</html>
```

---

## Style Lookup Table

Map the user's Q2 style choice to exact CSS values. Use the Google Fonts URL form in the `<link>` tag (replace spaces with `+`).

| slug | bg | heading | body | accent | heading_font | body_font |
|---|---|---|---|---|---|---|
| `editorial-dark` | `#111111` | `#f5f0e8` | `#c8c4bc` | `#c9a14a` | Playfair Display | Inter |
| `clean-corporate` | `#ffffff` | `#1a2b4a` | `#4a5568` | `#2563eb` | IBM Plex Sans | IBM Plex Sans |
| `playful-startup` | `#0f0e17` | `#fffffe` | `#a7a9be` | `#ff8906` | Space Grotesk | Space Grotesk |
| `luxury-minimal` | `#f5f0e6` | `#1a1a1a` | `#3d3d3d` | `#8b7355` | Cormorant Garamond | Montserrat |
| `bold-mono` | `#000000` | `#ffffff` | `#e0e0e0` | `#39ff14` | Courier Prime | Courier Prime |
| `soft-pastel` | `#f8f6ff` | `#2d2b55` | `#4a4870` | `#8b80c4` | Nunito | DM Sans |
| `architecture` | `#1c2128` | `#e8e3d8` | `#b0aa9e` | `#d4a853` | Josefin Sans | Josefin Sans |
| `tech-keynote` | `#0a0a14` | `#e0e8ff` | `#8899bb` | `#4169e1` | Rajdhani | Inter |
| `nature-earth` | `#f5f0e8` | `#2d4a2d` | `#4a5a42` | `#6b8f4e` | Lora | Raleway |
| `cinematic` | `#0d0d0d` | `#f0e8d8` | `#b8aa99` | `#d4892a` | Bebas Neue | Source Serif 4 |

If the user provides a URL: use WebFetch to extract dominant colors + fonts and map to nearest style or construct a custom set.

If the user describes in words: map to nearest entry or interpolate.

---

## Design Principles

*Adapted verbatim from Jack Roberts' power-design skill (MIT, https://github.com/ItsssssJack/power-design). Every rendered slide must satisfy all applicable rules.*

1. **One idea per slide.** Every slide carries exactly one idea. If you feel the urge to add a second point, make a second slide.

2. **Glanceable in ≤3 seconds.** If the audience can't grasp the point within 3 seconds of seeing it, simplify it.

3. **Cognitive load ≤7±2 chunks, ideally 3–5.** A "chunk" is one headline, one callout, one data label, or one bullet. Never exceed 7 on any slide.

4. **Whitespace ≥40% of slide area.** Cover slides: ≥60%. Whitespace is not emptiness — it is emphasis and breathing room.

5. **5% safe-zone on all edges.** No text or critical content within 64px of any edge on a 1280×720 canvas.

6. **Modular type scale (ratio 1.25–1.618).** Example at 1.333: body 24px → subhead 32px → title 42px → display 56px. Never skip more than one step.

7. **Maximum 4 type sizes per deck.** Pick a scale and stick to it for the entire deck.

8. **Body ≥24px, titles ≥48px.** Never set body copy below 24px or titles below 48px.

9. **Line-height 1.4–1.6 for body, 1.05–1.2 for display.** Tight display, loose body.

10. **Line length ≤60ch.** No line of body copy should exceed 60 characters.

11. **WCAG contrast ≥4.5:1, aim for 7:1.** Check every text/background pair. If uncertain, go darker or lighter.

12. **60-30-10 colour split.** 60% dominant (usually background), 30% secondary (body text), 10% accent.

13. **One accent colour per slide.** Two accents on one slide means no accent — the eye has nowhere to land.

14. **Never encode meaning by hue alone.** Add shape, label, or position cues for colourblind accessibility.

15. **8pt grid.** All spacing values must be multiples of 8: 8, 16, 24, 32, 48, 64, 96, 128px.

16. **Align to one grid axis per slide.** Choose left-aligned or centred — mixing both without intent looks like an accident.

17. **Proximity: related ≤16px apart, unrelated ≥48px apart.** Proximity communicates relationship.

18. **Data-ink ratio ≥80%.** Remove every gridline, border, and colour fill that does not carry information.

19. **F-pattern: headline and key visual in the top-left zone.** Western readers scan F-shaped. Primary message goes where the eye lands first.

20. **Pick Tufte mode or Reynolds mode — and stay.** Tufte: data-forward, minimal decoration. Reynolds: narrative-forward, full-bleed images, minimal text. Never mix within a deck.

---

## Slide Type Guide

| Type | Rules |
|---|---|
| **Cover** | No bullets. Headline ≥56px, tagline ≤12 words. ≥60% whitespace. Logo if provided. |
| **Content** | One headline. 3–5 bullets each ≤14 words. Logo if provided. |
| **Comparison** | Two-column layout, label each column, accent divider between them. |
| **Quote** | Single quote ≤40 words. Attribution in accent colour. Minimal decoration. |
| **Data/Stat** | Headline states the insight (not "Sales Q3"). Stat callouts with large number + label. No decorative borders. |
| **Image** | Image fills ≥50% of slide. Text overlay ≤10 words with high-contrast background behind text. |
| **Closing** | Mirror the cover style. Clear CTA or next step. Contact details if requested. |

---

## What This Skill Does NOT Do

- Generate images (uses user-supplied URLs or CSS-only visuals)
- Connect to third-party APIs or use your API keys
- Create PPTX files (HTML + browser Print-to-PDF is the output)
- Auto-publish to the web
- Invent content — from-pdf and from-url modes only distil what is in the source, never fabricate

---

*quick-deck v1.1 | https://cwspoeticartistry.github.io/quick-deck/ | MIT | Credits: Jack Roberts (power-design)*
*Bundled: deck-from-pdf + deck-from-url (no extra keys required)*
