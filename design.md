# quick-deck Design System

Research sources: designfly.io (Firecrawl scrape + Playwright screenshots 2026-05-16), anthropic.com, claude.com.
This file is the single source of truth for all visual decisions in `docs/`.

---

## 1. Brand Foundations

### Colors

All values are CSS custom properties on `:root`.

| Token | Hex | Usage |
|-------|-----|-------|
| `--bg` | `#faf9f5` | Page background (cream white) |
| `--bg-warm` | `#eeece2` | Card fills, section bands, hero band |
| `--bg-deep` | `#e8e6dd` | Expanded card detail bg, code blocks |
| `--bg-invert` | `#1a1915` | Footer background |
| `--ink` | `#3d3929` | Body text, paragraph |
| `--ink-strong` | `#141413` | Headlines, labels, nav |
| `--ink-mute` | `#6b6759` | Captions, meta, placeholder text |
| `--ink-invert` | `#faf9f5` | Text on dark footer |
| `--coral` | `#da7756` | Accent — use as punctuation colour only (<5% of any surface) |
| `--coral-deep` | `#bd5d3a` | Primary CTA button background |
| `--coral-hover` | `#a8512f` | Primary button hover state |
| `--rule` | `#d8d5c7` | Borders, hairlines, dividers |
| `--rule-strong` | `#bdbaa8` | Focused card border, active state border |

**Rules:**
- Coral is never used as a section or card background fill. It appears on CTAs, links, icons, and punctuation dots.
- Cards separate from the page via `--bg-warm` fill + `1px solid --rule`, not shadows.
- The footer flips to `--bg-invert` with `--ink-invert` text. Coral still works as accent here.

### Typography

#### Font stack

| Role | Family | Weights | Source |
|------|--------|---------|--------|
| Display / H1 | Fraunces (variable) | 400, 600 | Google Fonts |
| Headings H2–H4 | Fraunces | 400, 600 | Google Fonts |
| UI / labels / buttons | Inter (variable) | 400, 500, 600 | Google Fonts |
| Body / long-form | Fraunces, ui-serif, Georgia, Cambria, "Times New Roman", serif | — | System stack |
| Code / mono | ui-monospace, "SF Mono", "Cascadia Code", Menlo, Consolas, monospace | — | System stack |

**Import URL (single request):**
```
https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,400;0,9..144,600;1,9..144,400&family=Inter:wght@400;500;600&display=swap
```

#### Type scale

Desktop → mobile (below 480px):

| Label | Desktop | Mobile |
|-------|---------|--------|
| Display / hero h1 | 56px | 40px |
| Section h2 | 40px | 32px |
| Card heading h3 | 28px | 24px |
| Sub-heading h4 | 20px | 18px |
| Body | 16px | 16px |
| Small / caption | 14px | 14px |
| Micro / label | 12px | 12px |

Line-heights: 1.1 for display, 1.3 for headings, 1.65 for body, 1.4 for small.
Letter-spacing: −0.02em for display, 0 for body, 0.05em for micro labels.

### Spacing

8px grid. All spacing values must be multiples of 8:

`8 / 16 / 24 / 32 / 48 / 64 / 96 / 128px`

Page max-width: `1120px`, centered, padding `0 32px` (desktop), `0 24px` (tablet), `0 16px` (mobile).

---

## 2. Components

### Buttons

#### Primary
- Background: `--coral-deep` → `--coral-hover` on hover
- Text: white, Inter 500, 15px
- Padding: `12px 24px`
- Border-radius: `8px`
- Border: none
- No transform, no shadow, no lift effect on hover

#### Secondary / Ghost
- Background: transparent → `--bg-warm` on hover
- Text: `--ink-strong`, Inter 500, 15px
- Border: `1.5px solid --ink-strong`
- Padding: `12px 24px`
- Border-radius: `8px`

#### Text link
- Color: `--coral-deep`
- No underline by default, underline on hover
- Used for in-body navigation (e.g., "View styles →")

### Install Card (collapsed state)

The two install-path cards are the hero interaction of the page.

```
┌─────────────────────────────────────┐
│  [Icon]  Path A — Claude Code       │
│                                     │
│  One-line description               │
│                                     │
│  [Get started →]                    │
└─────────────────────────────────────┘
```

- Fill: `--bg-warm`
- Border: `1px solid --rule`
- Border-radius: `16px`
- Padding: `32px`
- Cursor: pointer
- `aria-expanded="false"`
- On hover: border color → `--rule-strong`
- No transform, no shadow

### Install Card (expanded / detail state)

When the user clicks a card, it expands **in place** to show numbered steps. The card grid hides the other card.

```
← back
────────────────────────────────────────
Path A — Claude Code

① Download the skill file
   [Download quick-deck.md ↓]

② Move it to your skills folder
   Mac/Linux:   ~/.claude/skills/quick-deck/SKILL.md
   Windows:  %USERPROFILE%\.claude\skills\quick-deck\SKILL.md
   [Copy Mac path]  [Copy Windows path]

③ Open Claude Code and type:
   ┌──────────────────────────────┐
   │ use quick-deck               │
   └──────────────────────────────┘
   [Copy trigger phrase]
────────────────────────────────────────
```

**Transition:** `max-height` from `0` to `auto` via a JavaScript-measured explicit `max-height` value, combined with `opacity` 0→1. Duration: `280ms`, easing: `cubic-bezier(0.2, 0.6, 0.2, 1)`. The collapsed card grid fades out as the detail panel fades in.

**Implementation note:** Use `data-expanded="true/false"` on the card container. JavaScript toggles this attribute. CSS transitions `max-height`, `opacity`, and `padding` on `.card-detail`.

### Mock Conversation Bubbles

Used in the "What to expect" section to preview the Q1/Q2/Q3 intake.

```
┌──────────────────────────────────────────┐  ← Claude bubble (--bg-warm bg)
│ What type of presentation would you      │
│ like me to create today?                 │
│ (assistant voice)                        │
└──────────────────────────────────────────┘

                                  [User reply] ─┐  ← User bubble (--bg bg)
                                                │
```

- Claude bubble: `--bg-warm` fill, Fraunces italic, rounded 16/16/16/4px corners
- User bubble: white fill, Inter, rounded 16/4/16/16px corners, right-aligned
- Font sizes: 15px / 14px, `--ink` color
- Max-width: 72% of container
- Stack vertically with 12px gap

### Feature Pills

Small stat/badge used in the features row:

```
· Any Claude
```

- `1px solid --coral`, border-radius `100px`
- `4px 12px` padding, Inter 12px, `--ink-strong` text
- Coral `·` dot as `::before` pseudo-element

### Copy Block

For CLI commands and install paths:

```
~/.claude/skills/quick-deck/SKILL.md        [Copy]
```

- Fill: `--bg-deep`
- Border: `1px solid --rule`
- Border-radius: `8px`
- Padding: `12px 16px`
- Font: monospace stack, 13px, `--ink-strong`
- Copy button: top-right, Inter 12px, `--ink-mute` → `--coral-deep` after click
- Copy feedback: button label → "Copied ✓" for 2000ms, then resets

### Download Button

Primary download action on the setup guide:

- Use `<a href="quick-deck.md" download>` — real anchor, not a JS button
- Styled as a primary button with a download icon (↓) prepended
- After download starts, show "Downloaded ✓" feedback for 1500ms

### FAQ Accordion

```
▸ Does it work with Claude.ai?     ← collapsed (border-bottom only)
▾ Do I need an API key?            ← expanded
  No. quick-deck uses only the Claude instance you're already using...
```

- `<details>/<summary>` HTML elements for full accessibility without JS
- Custom `list-style: none` + `::marker` removed
- `summary` uses Inter 500, `--ink-strong`, `16px`
- Answer uses Fraunces body, `--ink`, `15px`, indented `16px`
- Expand/collapse arrow via CSS `::after` on `summary`, rotates `0° → 90°`
- Border-bottom: `1px solid --rule` on each item

### Footer Strip

- Background: `--bg-invert`
- Text: `--ink-invert`, opacity 0.6 for secondary items
- One "Free forever · Open source" pill: `1px solid rgba(255,255,255,0.25)`, `--ink-invert` text
- GitHub icon link: simple SVG, `--ink-invert`
- Attribution line: "Built on Jack Roberts' power-design (MIT)" — small, muted

---

## 3. Motion

### Principles (from Anthropic brand)

- All transitions ≤ 280ms. Nothing slow and weighty.
- Easing: `cubic-bezier(0.2, 0.6, 0.2, 1)` for enter, `ease-out` for hover states.
- No parallax. No auto-playing carousels. No scroll-jacking.
- Animation is functional, not decorative.

### Scroll fade-in

Major sections (hero, features, install, what-to-expect, styles CTA, pdf steps, faq) use `IntersectionObserver` with:

```js
{ threshold: 0.1, rootMargin: '0px 0px -32px 0px' }
```

Each section starts at `opacity: 0; transform: translateY(8px)` and transitions to `opacity: 1; transform: translateY(0)`.

### `prefers-reduced-motion`

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

All `IntersectionObserver` callbacks check `window.matchMedia('(prefers-reduced-motion: reduce)').matches` and skip the animation if true, making content immediately visible.

---

## 4. Responsive Breakpoints

| Breakpoint | Max-width | Changes |
|-----------|-----------|---------|
| Desktop | ≥ 1200px | 1120px content, 2-col install cards, 3-col style grid |
| Tablet | 768–1199px | 880px content, 2-col install cards stack, 2-col style grid |
| Phablet | 480–767px | 100% content, install cards single column, style grid 1-col, nav collapses |
| Mobile | < 480px | Same, tighter padding (16px), type scale drops one step |

### Nav collapse

On < 480px: wordmark stays left, nav links hidden by default, a hamburger `<details>/<summary>` pattern shows them. No JS required.

---

## 5. Accessibility

- **Contrast:** All text/background combos must pass WCAG AA (4.5:1 for body, 3:1 for large text). Verify in Chrome DevTools colour picker.
  - `--ink` (#3d3929) on `--bg` (#faf9f5): ≈ 10:1 ✓
  - `--ink-strong` (#141413) on `--bg-warm` (#eeece2): ≈ 14:1 ✓
  - White on `--coral-deep` (#bd5d3a): ≈ 4.7:1 ✓ (button text)
  - `--ink-mute` (#6b6759) on `--bg` (#faf9f5): ≈ 4.8:1 ✓
- **Focus ring:** `outline: 2px solid var(--coral); outline-offset: 3px` on all interactive elements.
- **Skip link:** `<a class="skip-link" href="#main">Skip to content</a>` — visible only on focus, positioned above nav.
- **Install cards:** `<button>` elements, `aria-expanded` toggles between `"true"/"false"`, `aria-controls` points to the detail panel `id`.
- **Detail panels:** `role="region"`, `aria-labelledby` pointing at the card heading `id`.
- **FAQ:** Native `<details>/<summary>` — keyboard accessible without extra ARIA.
- **Download link:** `<a href="quick-deck.md" download>` — let the browser handle the semantics.
- **Copy buttons:** `aria-label="Copy to clipboard"`, update `aria-label` to "Copied" for 2 seconds after success.
- **Images:** All decorative images get `alt=""`, functional images get descriptive alt text.

---

## 6. What NOT to Do

- **No drop shadows on cards.** Cards separate by fill + border only. Box-shadow makes the page feel heavy.
- **No coral as a section fill.** Coral is punctuation. Using it as a 100% background fill destroys the restraint of the palette.
- **No parallax or scroll-jacking.** The page scrolls at 1:1. The user controls the viewport.
- **No heavy transforms (scale, rotate) on hover.** Hover states change border colour or background tint only.
- **No blocking Google Fonts fallback.** Use `font-display: swap` (already default for Google Fonts `&display=swap` URL).
- **No Anthropic logos, wordmarks, or the Copernicus/Styrene B proprietary fonts.** We use Fraunces + Inter as ethical substitutes.
- **No user credentials in any file in `docs/`.** The credentials grep before every push is a hard gate.
- **No `C:\Users\Dell\...` paths in public-facing content.** Reference paths generically as `~/.claude/skills/quick-deck/` or `%USERPROFILE%\.claude\skills\quick-deck\`.
- **No trailing empty pages in PDF export.** Ensure `.slide { page-break-after: always; }` is combined with `@page { margin: 0; }` on the rendered deck HTML, not this setup page.

---

## 7. Designfly.io Structural Inspiration

Adopted patterns (not copied code or assets):
- **Opinion hero:** Single declarative sentence as H1 ("Designing world-class products in California" → we adapt to quick-deck context)
- **Device mockup gallery:** We render Claude conversation mock instead of product screenshots
- **Credits/steps breakdown:** We use a numbered step list inside expanded cards, not a pricing table
- **Scarcity/availability pill near footer:** We use "Free forever · Open source" instead

Not adopted:
- Pricing tables, subscription flows, Webflow interactions, glass morphism backgrounds, dark colour scheme

---

*Last updated: 2026-05-16. Update this file before editing any file in `docs/`.*
