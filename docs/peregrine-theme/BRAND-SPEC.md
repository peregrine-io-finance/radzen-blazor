# Peregrine Brand Spec — Design Inputs for the Radzen Theme

> **⚠ Strategy superseded 2026-06-16.** The theme now targets **full product-match** (see
> **[`peregrine-scss-plan.md`](peregrine-scss-plan.md)**), not the "hybrid" described below. The
> **"Product design system" values in this doc remain valid** and were corroborated by live captures; the
> marketing-brand inputs (Bright Orange primary, Protocol/Kale fonts, square radius, navy dark) are **no longer
> the direction**. Use this doc for product values, not for strategy.

Status of every design input feeding the `peregrine` theme, with values verbatim where known. Sourced 2026-06-10 from the Notion workspace, SharePoint, and the live product app; verified by an adversarial second pass. Strategy (decided): **hybrid** — 2026 brand identity for fonts and primary colors, product UI Kit for UI semantics.

## Source-of-truth map

| Input | Source | Status |
|---|---|---|
| Brand color palette (2026) | Brand Guidelines PDF v1 (58 pp) | **EXTRACTED** (2026-06-10, text layer) — local copy: `C:\Users\GarrettWomack\OneDrive - Peregrine Technologies\Financial Statements\Claude Power Point\Peregrine Brand Guidelines [Notion].pdf`; full text dump: workspace `brand/pdf-text.txt` |
| Typography roles (Protocol vs Kale usage, sizes, weights) | Brand Guidelines PDF type hierarchy + peregrine.io live CSS | **EXTRACTED** — both below; Web styleguide Figma remains optional corroboration |
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

## Brand Guidelines PDF v1 — authoritative palette & typography

Extracted from the embedded text layer of the 58-page guideline (pages cited). Where the PDF and peregrine.io disagree, see the reconciliation table below.

### Primary colors (p. 22)

"Black and Off-White form the foundation … The Blue range introduces depth, hierarchy, and adaptability across product surfaces, while Bright Orange functions as a high-visibility accent used sparingly for emphasis or critical actions."

| Name | Hex | RGB | Role |
|---|---|---|---|
| Black | `#111111` | 17, 17, 17 | Foundation text/contrast |
| Off-White | `#FFFEF9` | 255, 254, 249 | Foundation light surface |
| Blue 0 | `#172935` | 23, 41, 53 | Darkest structured blue — dark surface anchor (= peregrine.io page bg) |
| Blue 1 | `#264757` | 38, 71, 87 | Dark blue |
| Blue 2 | `#9DC9DA` | 157, 201, 218 | Light blue |
| Blue 3 | `#E2EFFF` | 226, 239, 255 | Palest blue tint |
| Bright Orange | `#FF6201` | 255, 98, 1 | **High-visibility accent — CTAs/critical actions only, used sparingly** |

### Secondary colors (p. 23)

Grays "for structuring layouts, defining components, and supporting typography"; olives for "warmth … backgrounds, illustrations, and data states that require differentiation"; Light Orange as "a softer accent for non-critical highlights."

| Name | Hex | Notes |
|---|---|---|
| Gray ramp (dark→light) | `#2B2926`, `#5A5955`, `#A5A49F`, `#DCDBD7`, `#EEEEEE` | PDF lists dark→light; numbering (Gray 1–5) inferred from listing order — confirm visually (the site names `#5A5955` "gray5") |
| Light Orange | `#FFDFBF` | Soft accent, non-critical highlights |
| Olive 1–3 | `#312617`, `#757459`, `#F4FFAD` | Warm family (site CSS names `#312617` "chocolate" and adds `#3D3C2A`/`#C5C3A5`) |

### Usage rules that constrain the theme (pp. 22–30, 53–54)

- Bright Orange: **never a background or decorative color**; reserved for CTAs, highlights, key data points, map emphasis.
- **"Do not use black or dark grays as background"** — dark-mode surfaces should anchor on the structured blues (Blue 0/Blue 1), exactly as the live site does. No gradients. No unapproved color combos.
- Pairings must meet **AA or AAA** contrast (p. 25 shows approved type/background pairs incl. orange→black text, light-orange→black).
- Data viz (p. 53): "Use Bright Orange to highlight the key value … When a single-hue palette (like blues with lighter tints) communicates hierarchy clearly, rely on shading and scale instead" → chart series = blue ramp + tints, orange as the emphasis series.
- Iconography (pp. 33–38): bespoke marketing icons on a 40×40 grid, explicitly **"not to replace functional UI icons within the product"** → keeping Material Symbols in the Radzen theme is consistent with the guideline.
- Logo: black or white only; min 40px lockup / 20px symbol; clearspace = symbol height.

### Type hierarchy (pp. 14–15, verbatim)

Protocol is "the primary typeface … Use Regular for body content, Semi-Bold for emphasis, and Bold for statements requiring greater urgency." Kale Sans Mono for "captions or tiny details … to emote technicality, expertise and precision" — never for headlines (p. 19 misuse list).

| Style | Face | Line height | Letter spacing |
|---|---|---|---|
| H1 | Protocol Regular | 90–100% | −2%–0% (numerals −4%–0%) |
| H2 | Protocol Regular | 90–100% | −1%–0 |
| Subhead | Protocol **Bold** | 100–110% | −1%–0 |
| Body | Protocol Regular | 120–130% | −1%–0 |
| Labels / Buttons / CTAs | Protocol **Semi-Bold** | — | −1%–0 |
| Caption | **Kale Sans Mono** Regular | 120–130% | −1%–0 |

Body-copy rules (p. 17): leading 120–130%, Regular for dense text, Bold sparingly for emphasis. Headlines left-aligned, no periods.

### PDF ↔ peregrine.io reconciliation

| Token | PDF (authoritative) | peregrine.io | Call |
|---|---|---|---|
| Brand orange | `#FF6201` | `#FF601C` (Courage), hover `#FF7621` | Use **`#FF6201`**; keep `#FF7621` as hover/light shade |
| Dark surface | Blue 0 `#172935` | `#172935` (`data-theme=modern` bg) | Agree |
| Light surface | Off-White `#FFFEF9` | `#FFFEF9` | Agree |
| Blues | Blue 0–3 | `--token-blue0/2/3` same hexes | Agree |
| Teal `#23C4B5` / blue `#0076A0` | **absent from PDF** | Collaboration (22 uses) / Trust (9 uses) | Website-only accents — candidates for `$rz-secondary`/`$rz-info`, needs a design call |
| Button weight | Semi-Bold (600) | 700 | Minor; prefer PDF's 600, verify visually |
| Radius | not specified (visuals are square) | `border-radius: 0` brand trait | Use 0 |

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

(Superseded where it conflicts with the PDF — see the reconciliation table above; the site remains the source for hover states, type scale pixel values, spacing tokens, easings, and font file URLs.)

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

## Consolidated Radzen token mapping (working values for Phase 1/2)

| Radzen token | Light | Dark | Source/rationale |
|---|---|---|---|
| `$rz-primary` | `#FF6201` (Bright Orange) | `#FF6201` (verify contrast on navy; site uses it on `#172935` heavily) | PDF: the action/emphasis color; hover/light shade `#FF7621` (site) |
| `$rz-secondary` | **design call**: Blue 1 `#264757` / Blue 2 `#9DC9DA` (PDF "structured blues") or Collaboration teal `#23C4B5` (site-only) | dark-adjusted same | PDF has no second accent; site uses teal |
| `$rz-info` | Trust `#0076A0` (site) or Blue 1 | lighter blue (e.g. Blue 2) | no PDF state colors |
| `$rz-success` / `$rz-warning` / `$rz-danger` | product palette `#1BA572` / `#D99919` / `#ED5F74` | product dark text variants (BRAND-SPEC product section) | hybrid strategy — brand defines no state colors |
| Body background | Off-White `#FFFEF9` | **Blue 0 `#172935`** (NOT black/gray — PDF rule) | PDF + site agree |
| Neutral ramp `$rz-base-*` | build from Gray ramp `#EEEEEE`→`#2B2926` + Off-White | build from Blue 0/Blue 1 + darkened steps (navy-tinted, not gray) | PDF grays (light) / PDF dark-bg rule (dark) |
| Text | `#111111` titles, `#2B2926`/`#5A5955` secondary tiers | Off-White / Blue 2 / Blue 3 tiers | PDF pairings, AA/AAA required |
| `$rz-border-radius` | `0` | `0` | site brand trait; PDF visuals square |
| `$rz-text-font-family` | `'Protocol', Arial, Helvetica, sans-serif` | same | PDF primary typeface; metric fallback Arial (site) |
| Headings | Protocol Regular (400), LH 0.9–1.0, LS −1/−2% | same | PDF type hierarchy — headings differ by size, not weight |
| Buttons/labels | Protocol Semi-Bold (600) | same | PDF (site uses 700 — verify visually) |
| Caption/overline | Kale Sans Mono 400, uppercase for "intel" labels | same | PDF + site; never headlines |
| `$rz-series-1..24` | blue ramp + tints (Blue 0–3 interpolated), Bright Orange as emphasis series, olives for differentiation | same hues dark-adjusted | PDF data-viz guidance (p. 53) |
| Shadows | subtle, flat (site's light shadow usage) | subtle | no PDF elevation language |

## Chart series derivation (implemented, Phase 5)

Per the PDF data-viz guidance (p. 53): Bright Orange leads as the emphasis series, the blue range carries hierarchy, olives differentiate.

**Light `$rz-series-1..8`** (base set): `#ff6201` (Bright Orange), `#264757` (Blue 1), `#9dc9da` (Blue 2), `#0076a0` (Trust), `#757459` (Olive 2), `#628899` (Blue 1↔2 midpoint), `#c5c3a5` (olive-sand), `#172935` (Blue 0). Series 9–16 = 25% white tints of 1–8; series 17–24 = 50% white tints.

**Dark `$rz-series-1..8`**: same logic with navy-invisible deep blues swapped out — `#ff6201`, `#9dc9da` (Blue 2), `#4d9fbd` (Trust lightened 30%), `#e2efff` (Blue 3), `#c5c3a5`, `#628899`, `#f4ffad` (Olive 3), `#ffdfbf` (Light Orange). Series 9–16 = 25% white tints; 17–24 = dimmed 35% toward Blue 0.

**Schemes**: `palette` routes through `var(--rz-series-1..8)`; `monochrome` is the single-hue Blue 0→Blue 3 ramp (light-first in dark mode); `divergent` runs structured blue → neutral → Bright Orange (bright ends, dim navy center in dark mode). The hardcoded sankey scheme lists were hoisted into `$rz-sankey-{palette,monochrome,divergent}-colors !default` in `_sankey.scss`; `$chart-color-schemes` was already overridable. Other themes' compiled CSS verified byte-identical.

## Remaining design calls

1. `$rz-secondary`: PDF blues vs site teal `#23C4B5` (see table) — affects secondary buttons, chips, selection accents.
2. Gray 1–5 numbering (assumed dark→light from PDF listing order; site names `#5A5955` "gray5") — confirm against PDF visuals before naming SCSS comments after them; hex values themselves are unambiguous.
3. Semantic state colors are inherited from the product palette, not brand-approved — sanity-check with #brand-design (brand-team@peregrine.io per PDF p. 58) if visibility matters.
4. Bright Orange as `$rz-primary` means orange spreads to everything Radzen colors "primary" (links, focus rings, active states, selection) — the brand wants orange *sparing*. Mitigation if it reads too loud: primary = Blue 1 with orange reserved for `$rz-secondary`/CTA-style buttons; decide during Phase 1 visual review.
