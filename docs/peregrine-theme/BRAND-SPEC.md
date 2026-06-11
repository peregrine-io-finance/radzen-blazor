# Peregrine Brand Spec — Design Inputs for the Radzen Theme

Status of every design input feeding the `peregrine` theme, with values verbatim where known. Sourced 2026-06-10 from the Notion workspace, SharePoint, and the live product app; verified by an adversarial second pass. Strategy (decided): **hybrid** — 2026 brand identity for fonts and primary colors, product UI Kit for UI semantics.

## Source-of-truth map

| Input | Source | Status |
|---|---|---|
| Brand color palette (2026) | Brand Guidelines PDF on SharePoint | **NOT YET RETRIEVED** — connector lacks `Sites.Read.All`; Garrett to drop the PDF in `C:\Users\GarrettWomack\source\ClaudeProjects\radzen\brand\`. Link: <https://peregrine65.sharepoint.com/:b:/s/Peregrine/IQB_mixtxr1sSIgEccL_rStRAWc0riczjKJgnvmmMM_lh_s?e=8kYo1r> |
| Typography roles (Protocol vs Kale usage, sizes, weights) | Brand Guidelines PDF + Web styleguide Figma (`figma.com/design/JKxgwt3mlqXbaHqxLDbllx`) + peregrine.io live CSS | PDF pending; peregrine.io extraction below |
| Font files | SharePoint (inventoried below) | Done |
| Product design system (UI semantics, dark mode reference) | Notion UI Kit pages (verbatim below) | Done |
| Logos | Notion "Logo" page (SVG/PNG variants) + SharePoint Logo + Icon folder | Locations known; assets not needed for CSS-token work |

The 2026 Notion brand page (`[2026] Peregrine Brand Resources`, `2ee93d43caca8060b778e4eea7d27165`) is a download hub only — verified to contain **zero** hex codes or typography specs. The only typography rule on it is the Microsoft-template fallback: "Arial — body copy and titles; Roboto Mono — highlight text (sparingly)". Brand questions: #brand-design (day-to-day) / #brand-design-review (approvals).

## Fonts (SharePoint inventory — exact)

Location: `Peregrine site → Shared Documents/Design/2026- Peregrine Brand Folder/Brand Assets/Notion Links for Brand Assets/Fonts/` (driveId `b!QO7O088Y5UaTA0YvC96SPDsPu7T8_HJDpyuMokRDl8QU8LsXDpWbQoV7y2-RiPcg`).

**APK Protocol** (folder `Protocol`, itemId `01OIBTM335PHYCQZHHMZGIDDKUUAWTOALK`) — **OTF only, web conversion required**:
- Regular, Regular-Italic, Semi-Bold, Semi-Bold-Italic, Bold, Bold-Italic (6 static styles)
- No EULA file present — **web-embedding/conversion/redistribution rights unverified** (blocker for shipping in `wwwroot/fonts/`)

**Kale Sans Mono** (folder `KALE SANS MONO`, itemId `01OIBTM35WRTLJTXTWDVCZT4ZRSDY7M2LL`):
- Regular in OTF + **WOFF + WOFF2** (web-ready as-is)
- **Regular only** — no bold/italic exists in any format
- License: `EULA Boulevard LAB 2025.pdf` in the folder (unread — check webfont clauses)

**Roboto Mono** (folder `Roboto Mono`): Regular TTF — Microsoft-template fallback font; if ever needed on web, take woff2 from Google Fonts.

Product app mono is **IBM Plex Mono** (open font) — see UI Kit tokens below. Font-role decision (headings/body/mono) pending PDF + peregrine.io.

## peregrine.io live-site extraction (2026-06-10)

Extracted from the production CSS (Next.js + Tailwind v4 + a `--token-*` design-token layer; two static chunks shared verbatim by `/`, `/foundation`, `/careers`, `/contact`). This is the canonical 2026 *web* expression of the brand and the primary palette source until the PDF confirms/extends it.

**Theme model:** the live site runs `<html data-theme="modern">` everywhere — a **dark navy theme** (`--background: #172935`, `--foreground: #fff`). A light theme exists in the CSS (`#fffef9` / `#1d1d1d`) but isn't active. No `prefers-color-scheme` — theming is attribute-driven.

**Brand color tokens** (named after company values; usage counts from `var()` references):

| Token | Hex | Uses | Role |
|---|---|---|---|
| `--token-courage` | **`#ff601c`** | 38 | **Primary brand orange** — CTAs, `::selection`, accents; hover `#ff7621` |
| `--token-collaboration` | `#23c4b5` | 22 | Secondary teal accent |
| `--token-trust` | `#0076a0` | 9 | Blue accent (info candidate) |
| `--token-stability-modern` | `#172935` | 4 | Dark-navy page background (live theme) |
| `--token-stability` | `#042e3e` | 4 | Deep petrol |
| `--token-black` | `#111` | 57 | Text / black surfaces |
| `--token-off-white` | `#fffef9` | 37 | Light surface / light-theme background |
| `--token-blue0/2/3` | `#172935` / `#9dc9da` / `#e2efff` | — | Blue ramp |
| `--token-gray5` | `#5a5955` | 1 | Warm gray text |
| `--token-chocolate`, `--token-olive/-1/-3` | `#312617`, `#757459`/`#3d3c2a`/`#c5c3a5` | — | Warm accent family |

Supporting neutrals/tints seen repeatedly: `#1d1d1d` (light-theme text), `#feeee3` (pale peach tint), `#ece3dc` (warm hairline border), borders at 20% alpha (`black/20`, `white/20`). Buttons: primary `#ff601c` bg / white text; ghost-on-dark `bg-black/16` or `off-white/20` with `border-white/20`.

**Typography** (all faces OTF, `font-display: swap`):
- Faces: `protocol` 400/600/700 + italics (= the 6 SharePoint OTFs); `kale` 400 only.
- **Body**: `var(--font-protocol), Arial, Helvetica, sans-serif`, weight 400.
- **Headings**: Protocol **400** (not bold) with tight leading — `.text-headline-h1` 48→80px, tracking −1px, line-height 0.95; h2 40→48px/1.05; h3 32→40px/1; h6 20→24px/1.15 (mobile→≥1280px).
- **Kale Sans Mono = label/"intel" font**: always uppercase, 10–14px (`.text-form-label` 14px, `.text-intel-large` 12px, `.text-intel-small`/`.text-pillbox` 10px).
- **Buttons**: Protocol **700**, 14px, line-height 1.1.
- Font scale tokens: 10, 12, 14, 16, 20, 24, 28, 32, 40, 48, 80px. Body base 14→16px.
- Metric fallbacks vs Arial: kale size-adjust 135.87%; protocol 102.21%.

**Other tokens:**
- **`border-radius: 0` is a brand trait** — buttons/cards are square; `modern:rounded-none` utilities; Tailwind radius defaults barely used.
- Shadows: light usage; `--drop-shadow-lg: 0 4px 4px #00000026`, hero `0 18px 40px -12px #00000080` — no Material-style elevation ramp.
- Spacing: 4px-based token scale (4…128px); breakpoints `phone:` ≥760, `tablet:` ≥1024, `desktop:` ≥1280; max content width 1550px.
- Easings: `--ease-out: cubic-bezier(.16,1,.3,1)`, sharp `(.2,0,0,1)`; default transition `.15s cubic-bezier(.4,0,.2,1)`.

**Public font files (HTTP 200, `font/otf`, served by peregrine.io itself — precedent for web-serving these exact files):**
```
https://peregrine.io/_next/static/media/APK_Protocol_Regular-s.p.6be8e497.otf
https://peregrine.io/_next/static/media/APK_Protocol_Regular_Italic-s.p.9c505791.otf
https://peregrine.io/_next/static/media/APK_Protocol_Semi_Bold-s.p.44ae2dfa.otf
https://peregrine.io/_next/static/media/APK_Protocol_Semi_Bold_Italic-s.p.2257843e.otf
https://peregrine.io/_next/static/media/APK_Protocol_Bold-s.p.7e00fdeb.otf
https://peregrine.io/_next/static/media/APK_Protocol_Bold_Italic-s.p.2732e69a.otf
https://peregrine.io/_next/static/media/KaleSansMono_Regular-s.p.e770d234.otf
```
(73–78 KB each; Kale 24 KB — small enough that woff2 conversion is an optimization, not a necessity.)

**Provisional Radzen mapping** (to confirm against the PDF):
- `$rz-primary: #ff601c` (Courage; hover/dark shade toward `#d35528`); `$rz-secondary: #23c4b5` (Collaboration); `$rz-info: #0076a0` (Trust)
- Light: body `#fffef9`, text `#1d1d1d`/`#111`; Dark: surfaces from `#172935` (the site's "modern" navy — anchor for the dark neutral ramp), text white
- success/warning/danger: no marketing-site equivalents — take from product palette (green `#1BA572`, yellow `#D99919`, red `#ED5F74`) per hybrid strategy
- `$rz-border-radius: 0` (sharp corners); `$rz-text-font-family: 'Protocol', Arial, Helvetica, sans-serif`; headings Protocol 400 tight-leading; Kale for overline/caption-style "intel" text

## Product design system (verbatim from Notion)

These are the product app's actual values — the reference for dark-mode relationships and UI semantics under the hybrid strategy. Cross-check against the PDF before promoting any to brand status (they may predate the 2026 refresh).

**Product Brand Colors** (Notion `7e8f35e4fdfc40318c284e8da8c631de`):

| Name | Hex |
|---|---|
| Blue | `#678EF1` |
| Green | `#1BA572` |
| Yellow | `#D99919` |
| Orange | `#E5734A` |
| Red | `#ED5F74` |
| Pink | `#D66FD6` |
| Purple | `#9671F0` |

**App CSS theme variables** (Notion `2e293d43caca80d5a4c1edfa03cd0d34`; values are `R, G, B`):

Light mode:
```
--theme-background-color-blue: 103, 142, 241   green: 27, 165, 114   yellow: 217, 153, 25
  orange: 229, 115, 74   red: 237, 95, 116   pink: 214, 111, 214   purple: 150, 113, 240
  default: 153, 161, 168   inverted: 74, 80, 84   white: 255, 255, 255
--theme-text-color-default: 31, 32, 33   blue: 65, 104, 203   green: 0, 127, 76
  yellow: 179, 115, 0   orange: 191, 77, 36   red: 199, 57, 78   pink: 176, 73, 176   purple: 112, 75, 202
--theme-surface-background-color-default: 255, 255, 255   low: 237, 240, 242   medium: 248, 249, 250   inverted: 31, 32, 33
--theme-divider-color-default: 28, 34, 38 (contrast .08/.12/.16)   opaque: 201, 209, 214
--theme-box-shadow-medium: 0 0 0 1px rgba(28,34,38,.1), 0 1px 1px rgba(28,34,38,.05), 0 2px 4px rgba(28,34,38,.1)
--theme-color-alias-canvas-background: 237, 240, 242   table-stripe: rgba(74,80,84,.04)
```

Dark mode (differences):
```
--theme-text-color-default: 255, 255, 255   blue: 133, 173, 247   green: 39, 194, 145
  yellow: 233, 183, 36   orange: 240, 146, 100   red: 245, 124, 147   pink: 231, 142, 231   purple: 181, 144, 247
--theme-surface-background-color-default: 31, 32, 33   low: 54, 57, 60   medium: 42, 44, 46
--theme-color-alias-canvas-background: 26, 27, 28
shadows: rgba(255,255,255,.2) ring + rgba(0,0,0,.5)
```

**UI Kit Tokens** (Notion `51031976a413474ca36d420e27ca1e01`) — sizes are multiples of a `GRID` unit whose base value is not defined on the page:
```typescript
borderRadius: { none: 0, small: GRID/2, default: GRID*0.75, large: GRID*1, xlarge: GRID*1.75 }
fontFamily:   { default: 'inherit', mono: "'IBM Plex Mono', monospace" }
fontSize:     { micro: 8, tiny: 10, small: 12, default: 13, large: 14, xlarge: 17 }
size:         { micro: GRID*2, tiny: GRID*2.5, small: GRID*3, default: GRID*3.25, large: GRID*4, xlarge: GRID*4.5 }
dividerContrast: { default: .08, low: .08, medium: .12, high: .16 }
boxShadow high (light): 0 0 0 1px S, 0 2px 4px S, 0 8px 24px S
```

Note the tensions with Radzen defaults the implementation must reconcile: product base font-size 13px vs `$rz-root-font-size: 16px`; product mono IBM Plex vs brand Kale; ring+blur shadows vs Material elevation stacks. Default per plan: keep Radzen's 16px root and Material elevation unless the PDF/peregrine.io contradicts.

## Logos (for reference; not required for CSS-token work)

- Notion "Logo" page (`3936af5a0cb9437bb8ead36255ec8c28`): all variants as PNG + **SVG** (lockup, logotype, mark-only, padded/unpadded); usage: black-on-transparent for light backgrounds, white-on-transparent for dark.
- SharePoint folder: `…/Brand Assets/Notion Links for Brand Assets/Logo + Icon` (<https://peregrine65.sharepoint.com/:f:/r/sites/Peregrine/Shared%20Documents/Design/2026-%20Peregrine%20Brand%20Folder/Brand%20Assets/Notion%20Links%20for%20Brand%20Assets/Logo%20+%20Icon?csf=1&web=1&e=pXDJRi>)
- Known-good PNG copies in Garrett's OneDrive: `Documents/Po Process/Logos/` (Peregrine-Logo-Black/White, Peregrine-Icon-Black/White).

## What the Brand Guidelines PDF must answer (extraction checklist)

When the PDF lands, extract verbatim into this doc:
1. Primary/secondary brand colors with hex values — and any full palette/ramp
2. Neutral/gray scale if defined
3. Typography: Protocol vs Kale roles, weights in use, type scale (sizes/line-heights), letter-spacing
4. Any dark-mode guidance (likely none — dark is a design exercise per plan)
5. Any spacing/radius/elevation language
6. Logo clearspace/min-size rules (reference only)
