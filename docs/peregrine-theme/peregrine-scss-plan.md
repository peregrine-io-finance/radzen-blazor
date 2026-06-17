# Peregrine Radzen Theme — SCSS Normalization Plan (implementation handoff)

> **Goal:** make the `peregrine` / `peregrine-dark` Radzen theme render Radzen components with the same look & feel as the **app.peregrine.io** product UI, in light and dark.
> **Status:** reference/prep doc — written 2026-06-16 from verified browser captures. Implementation is to be done in a fresh context window. **No SCSS has been changed yet.**
> **This supersedes the "hybrid" strategy in `BRAND-SPEC.md`.** Decision is now **full product-match**: drop the 2026 marketing-brand direction (Bright Orange primary, Protocol/Kale fonts, square radius, giant display headings). Keep BRAND-SPEC only as corroboration for the *product* values.

---

## 0. What the product actually is (read this first)

The live product is **not** Blueprint-classed and is **not** Material. Its UI is styled by a **global `--theme-*` CSS custom-property token layer** rendered through **styled-components + atomic utility classes** (Blueprint / react-mosaic / Mapbox GL / CodeMirror are peripheral widget libraries only). 

Consequences for this work:
- The **authoritative source of truth is the captured `--theme-*` token layer** (decoded in §2). It was verified **byte-consistent (0 value drift) across 12 captures** — 6 routes (`/home`, pipelines, instances, ontology, properties, manifest) × light/dark.
- Build the Radzen neutral ramp by **interpolating the captured product surfaces**, *not* from Blueprint's gray scale.
- **Per-component pixel values are not capturable** (styled-components hashed classes like `.jfYMUW` — no stable selectors). We do not chase them; Radzen components derive from the foundation, and component density/sizing comes from the locked decisions + visual QA. See §8.

Capture files (JSON; each has `meta`, `customProperties`, `tokenRules`, `anchors`, `fonts`, `stylesheets`):
**`docs/peregrine-theme/captures/`** — 12 files: `peregrine-capture-{light,dark}.json` + `-bolt-gw-test-org-<route>-{light,dark}.json`. Raw stylesheet text was stripped to keep the repo light (see `captures/README.md`); the decoded values in §2 are self-sufficient if the captures are ever removed.

---

## 1. Locked decisions

| # | Aspect | Value |
|---|---|---|
| 1 | Root font size | **14px** (`$rz-root-font-size`) |
| 2 | Fonts | **Inter** (UI/body), **IBM Plex Mono** (mono, caption/overline) |
| 3 | Corner radius | **3px** (`$rz-border-radius`); chip/group-header stay `calc(4 * radius)` = 12px |
| 4 | Primary | `#678EF1`, on-primary `#fff` |
| 5 | Info | `#678EF1` (product uses the blue family for info/links) |
| 6 | Success / Warning / Danger | light `#1BA572` / `#D99919` / `#ED5F74`; dark `#27C291` / `#E9B724` / `#F57C93` |
| 7 | Secondary | neutral grey `#99A1A8` (product has no distinct secondary) |
| 8 | Links | `#4168CB` (light) / `#85ADF7` (dark) |
| 9 | Neutral `$rz-base-*` ramp | interpolate from captured product surfaces (§4.3) — **not** Blueprint |
| 10 | Text tiers | alpha over surface — 100 / 66 / 50 / 33 % (default / secondary / tertiary / disabled) |
| 11 | Shadows | product ring+drop, incl. the `0 0 0 1px` hairline ring; heavier alphas in dark (§4.5) |
| 12 | Headings | match product: bold **700**, line-height **1.4**, modest sizes (h1 40 / h2 28 / h3 24 / h4 20 / h5 16 / h6 14 px) — replaces brand 400-weight `clamp()` scale |
| 13 | Body text | `1rem` = **14px** (rides the root) |
| 14 | Icon size | `$rz-icon-size` ≈ **1.15rem** (~16px) — product icons are ~16px (Radzen default is 1.5rem/24px) |
| 15 | Chart series `$rz-series-*` | product 7-hue palette + tints (§4.7) — overrides the brand series |

---

## 2. Decoded product token system (both modes)

All values are from the verified `--theme-*` layer. RGB triplets are stored for use as `rgb(var(--x) / a)`; hex given for convenience. Junk tokens ignored: `--theme-get`, `--theme-button-background`, `--theme-mobile`, `--theme-is-dark`, `--theme-name`, and empty placeholders (`--btn-bg`, `--knob-size`, …).

### 2.1 Surfaces / elevation
| Product token | Light | Dark |
|---|---|---|
| `canvas-background` (page) | `#EDF0F2` | `#1A1B1C` |
| `surface ...-default` (cards/panels) | `#FFFFFF` | `#1F2021` |
| `...-medium` / `...-top` (elevated) | `#F8F9FA` / `#FFFFFF` | `#2A2C2E` / `#2A2C2E` |
| `...-low` (inset / hover) | `#EDF0F2` | `#36393C` |
| `...-bottom` | `#F8F9FA` | `#1F2021` |
| `...-inverted` | `#1F2021` | `#FFFFFF` |
| `input-background` | `#FFFFFF` | `rgba(0,0,0,.5)` |

### 2.2 Text
| Role | Light | Dark |
|---|---|---|
| default (`text-color-default`) | `#1F2021` | `#FFFFFF` |
| secondary (~66%) | `rgba(31,32,33,.66)` | `rgba(255,255,255,.66)` |
| disabled (~33%) | `rgba(31,32,33,.33)` | `rgba(255,255,255,.33)` |
| link / blue | `#4168CB` | `#85ADF7` |

### 2.3 Semantic palette — solid fill is mode-independent; text shade shifts per mode
| Hue (role) | Solid fill | Text light | Text dark |
|---|---|---|---|
| blue (**primary / info**) | `#678EF1` | `#4168CB` | `#85ADF7` |
| green (**success**) | `#1BA572` | `#007F4C` | `#27C291` |
| yellow (**warning**) | `#D99919` | `#B37300` | `#E9B724` |
| red (**danger**) | `#ED5F74` | `#C7394E` | `#F57C93` |
| orange | `#E5734A` | `#BF4D24` | `#F09264` |
| purple | `#9671F0` | `#704BCA` | `#B590F7` |
| pink | `#D66FD6` | `#B049B0` | `#E78EE7` |
| neutral default (**secondary**) | `#99A1A8` | — | — |

### 2.4 Dividers / borders
| | Light | Dark |
|---|---|---|
| hairline (`divider-default`) | `#1C2226` @ 8 / 12 / 16% | `#FFFFFF` @ 8 / 12 / 16% |
| opaque (`divider-opaque`) | `#C9D1D6` | `#4A5054` |

### 2.5 Shadows (ring + soft drop)
| Level | Light | Dark |
|---|---|---|
| low | `0 0 0 1px rgba(28,34,38,.14)` | `0 0 0 1px rgba(255,255,255,.2)` |
| medium | `0 0 0 1px rgba(28,34,38,.1), 0 1px 1px rgba(28,34,38,.05), 0 2px 4px rgba(28,34,38,.1)` | `0 0 0 1px rgba(255,255,255,.2), 0 1px 1px rgba(0,0,0,.5), 0 2px 4px rgba(0,0,0,.5)` |
| high | `0 0 0 1px rgba(28,34,38,.1), 0 2px 4px rgba(28,34,38,.1), 0 8px 24px rgba(28,34,38,.1)` | `0 0 0 1px rgba(255,255,255,.2), 0 2px 4px rgba(0,0,0,.5), 0 8px 24px rgba(0,0,0,.5)` |

### 2.6 Aliases
| | Light | Dark |
|---|---|---|
| modal backdrop | `rgba(28,34,38,.4)` | `rgba(0,0,0,.55)` |
| table stripe | `rgba(74,80,84,.04)` | `rgba(255,255,255,.03)` |
| tab-list bg | `rgba(28,34,38,.1)` | `rgba(0,0,0,.5)` |

### 2.7 Typography (captured)
- Family: **Inter** everywhere; mono **IBM Plex Mono** (loaded 400/500/600/700).
- h1 = 40px / **700** / lh 1.4; h2 = 28px / **700** / lh 1.4; body = 13px / 400 / lh 1.3; link = `#4168CB`; bare `label` = 13px/500.
- h3–h6 were not rendered on captured pages → interpolated in §4.6.

---

## 3. Target files & how they relate

A Radzen theme = 6 files in `Radzen.Blazor/themes/`. For Peregrine:

| File | Role | Edit? |
|---|---|---|
| `peregrine.scss` | light entry | **yes — lockstep with base** |
| `peregrine-base.scss` | light, runtime-loaded | **yes** |
| `peregrine-dark.scss` | dark entry | **yes — lockstep with base** |
| `peregrine-dark-base.scss` | dark, runtime-loaded | **yes** |
| `peregrine-wcag.scss` | light AA/AAA variant | **regenerate** (see §6) |
| `peregrine-dark-wcag.scss` | dark AA/AAA variant | **regenerate** (see §6) |

- **Entry ≈ base**: `peregrine.scss` and `peregrine-base.scss` are near-identical (differ by ~6 lines: a `$base: true;` flag + a `// Gantt` block). Apply every foundation edit to **both**; same for the dark pair.
- **Mechanics**: components consume only `var(--rz-*)`; themes set `$rz-*` / `$component-*` Sass vars that `_css-variables.scss` flattens into `:root`. Set the **foundation** correctly and the hundreds of component vars (already written as `var(--rz-base-N)`, `var(--rz-primary)`, `var(--rz-shadow-N)`, …) inherit automatically.
- **Ramp direction convention**: `$rz-base-50` = lightest, `$rz-base-900` = darkest in *both* themes. In light, text pulls from the dark end (800/900) and surfaces from the light end; in **dark it inverts** — `$rz-text-color` = `--rz-base-50` (light) and `$rz-base-background-color` = `--rz-base-800`, body = `--rz-base-900`. Preserve this when filling the dark ramp (§4.3).
- Light base currently lacks an explicit `$rz-base-background-color` and `$base-styles-map` that the dark file has — **verify/normalize** these during implementation.

---

## 4. `$rz-*` mapping (the implementation)

> Strategy: change the **base inputs** only; keep the theme's existing derivation formulas (`mix()`, `rgba()`) and component vars so everything downstream recomputes. Then visually QA.

### 4.1 Theme constants (both light & dark unless noted)
```
$rz-border-radius:      3px;
$rz-root-font-size:     14px;
$rz-body-font-size:     1rem;          // = 14px
$rz-body-line-height:   1.4;
$rz-text-font-family:   'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;
$rz-body-background-color: light #EDF0F2 / dark #1A1B1C;   // canvas
$rz-base-background-color: light #FFFFFF / dark #1F2021;   // surface (cards/panels)
$rz-icon-size:          1.15rem;       // ~16px
$rz-outline-color:      var(--rz-primary);
```

### 4.2 Semantic colors (set the base hue; keep existing `-light/-lighter/-dark/-darker` formulas)
```
$rz-primary:   #678EF1   (both modes)
$rz-secondary: #99A1A8   (both modes)
$rz-info:      #678EF1   (light) / lighten for dark if needed
$rz-success:   #1BA572 (light) / #27C291 (dark)
$rz-warning:   #D99919 (light) / #E9B724 (dark)
$rz-danger:    #ED5F74 (light) / #F57C93 (dark)
```
**`on-*` contrast pairs must be re-derived** for the new palette (they are hand-set, not formula-based). Targets AA (≥4.5:1). NOTE: white on `#678EF1` ≈ 2.8:1 (fails AA). The **base theme matches the product (white text on the blue fill)**; the **`-wcag` variant** is where AA-safe choices live (e.g. on-primary → black, or darken primary). See §6.

### 4.3 Neutral ramp (proposed starting point — tune in QA)
**Light** (50 = lightest → 900 = darkest):
```
50 #F7F9FA  100 #EDF0F2  200 #E1E6EA  300 #C9D1D6  400 #99A1A8
500 #767E86 600 #565E66  700 #3C4248  800 #292D31  900 #1F2021
light #EDF0F2  lighter #FFFFFF  dark #565E66  darker #1F2021
```
Anchors: 100 = canvas `#EDF0F2`, 300 = opaque divider `#C9D1D6`, 400 = neutral `#99A1A8`, 900 = text `#1F2021`. (50 = grid-stripe, 100 = hover.)

**Dark** (50 = lightest → 900 = darkest; text uses 50, surfaces use 700–900):
```
50 #FFFFFF  100 #D5D8DB  200 #A7ADB3  300 #6B7177  400 #4A5054
500 #3A3E42 600 #36393C  700 #2A2C2E  800 #1F2021  900 #1A1B1C
light #D5D8DB  lighter #FFFFFF  dark #1F2021  darker #141516
```
Anchors: 50 = text `#FFFFFF`, 400 = opaque divider `#4A5054`, 600 = hover/low `#36393C`, 700 = elevated `#2A2C2E`, 800 = surface `#1F2021`, 900 = canvas `#1A1B1C`.

### 4.4 Text tiers (alpha over surface)
```
Light:  title/color #1F2021 ; secondary rgba(31,32,33,.66) ; tertiary rgba(31,32,33,.50) ; disabled rgba(31,32,33,.33)
Dark:   title/color #FFFFFF ; secondary rgba(255,255,255,.66) ; tertiary rgba(255,255,255,.50) ; disabled rgba(255,255,255,.33)
```
Links: `$rz-link-color` (or equivalent) = `#4168CB` light / `#85ADF7` dark.

### 4.5 Shadows ($rz-shadow-0..10 — ring on every level)
Anchor to the captured set: `0` = none, `1` = product **low**, `3` = product **medium**, `6` = product **high**. Fill `2` (between low/medium), `4`–`5` (between medium/high), and `7`–`10` by extrapolating the high shadow with progressively larger final blur (e.g. `…, 0 12px 32px`, `0 16px 48px`, `0 24px 64px`) while keeping the `0 0 0 1px` ring. Use the §2.5 dark column (white ring + `rgba(0,0,0,.5)` drops) for the dark files.

### 4.6 Typography map (`$text`, at 14px root)
Replace the brand `clamp()`/400-weight scale with:
```
h1 2.857rem(40px)/700/lh1.4   h2 2rem(28px)/700/lh1.4   h3 1.714rem(24px)/700/lh1.4
h4 1.429rem(20px)/700/lh1.4   h5 1.143rem(16px)/700/lh1.4 h6 1rem(14px)/700/lh1.4
display-h1..h6: mirror h1..h6 (or 1 step larger)   letter-spacing: 0 (drop brand negative tracking)
subtitle1 1rem/600   subtitle2 .875rem/600
body1 1rem/400/lh1.4   body2 .875rem/400/lh1.4
button .875rem/500/lh1.3/text-transform:none
caption  'IBM Plex Mono' .75rem/400/lh1.4
overline 'IBM Plex Mono' .6875rem/400/uppercase/letter-spacing .08em
```
Heading color = `var(--rz-text-title-color)`; body = `var(--rz-text-color)`.

### 4.7 Chart series (`$rz-series-1..24`)
Base hues 1–8 = `#678EF1, #1BA572, #D99919, #E5734A, #ED5F74, #D66FD6, #9671F0, #99A1A8`. Series 9–16 = ~20% white tint of 1–8; 17–24 = ~40% tint (light). For dark, swap in the dark text-shade variants for the deep hues and dim the tints toward the canvas. (Mirror the *method* already in the brand series block; just change the hues.)

---

## 5. Derivation rules (so a fresh window reproduces values)
1. Semantic `-light/-lighter/-dark/-darker`: **keep the file's existing formulas** (`mix($rz-white, $c, 20%)`, `rgba($c, .12–.20)`, `mix($rz-black, $c, 20/32%)`). Only the base hue changes.
2. `on-*` pairs: pick black/white (or a tint) to clear **AA 4.5:1** on each fill in the **wcag** files; in the **base** files match the product (white on blue, etc.).
3. Neutral ramp: monotonic interpolation between the §4.3 anchors in sRGB.
4. Text tiers: alpha of the default text color (§4.4).
5. Shadows: §4.5.

---

## 6. WCAG variants (method resolved)

**Structure.** `peregrine-wcag.scss` / `peregrine-dark-wcag.scss` are **not** SCSS-var themes — they are **flat, hand-authored CSS files**: one block `:root, .rz-peregrine { … }` (`.rz-peregrine-dark` for dark) listing **82 final `--rz-*` custom properties** with literal hex/rgba values (base ramp, each semantic + `-light/-lighter/-dark/-darker`, and every `on-*` pair). They override only the palette — no component/structural CSS, no `$` vars.

**No generator exists** (repo searched). They are recomputed by hand — commit `e8e89e55` did exactly this for the brand palette. To regenerate for the product palette:
1. Keep the 82-key skeleton, key order, and the `:root, .rz-peregrine[-dark]` selector from the current files.
2. For each color, start from the product value (§2/§4) and **adjust until every text-bearing pair clears AA (≥ 4.5:1)** at its usage site:
   - **Light** — hues that also serve as *text/icons* must darken on white. Primary `#678EF1` ↔ white ≈ **3.1:1** (fails normal-text AA; the ratio is symmetric, so blue-text-on-white and white-text-on-blue-fill are both ~3.1:1) → darken the wcag primary toward e.g. `#3B63C9` (~5.5:1). **Warning** `#D99919` on white ≈ 2.0:1 → drop to a deep amber (~`#946811`). Info / links / success / danger likewise darkened to text-safe shades.
   - **Dark** — *brighten* hues so colored text clears the dark cards (mirror the old file's orange/Trust brightening on navy).
   - **`on-*` pairs** — choose `#fff` / `#111` per fill for ≥ 4.5:1. Since white on `#678EF1` is only ~3.1:1, the wcag file either darkens the primary fill until white passes **or** sets `--rz-on-primary: #111`. (The base/non-wcag theme keeps the product's white-on-`#678EF1`; **AA is enforced only in the wcag files**.)
3. Neutral ramp = the product cool-grey ramp (§4.3), nudged only where a text tier misses AA.
4. Verify every `--rz-on-*`, text, and link value with a contrast checker; test at `?theme=peregrine&wcag=true` and `?theme=peregrine-dark&wcag=true`.

This is mechanical contrast math, **not a capture** — the product app can't supply it (the product isn't itself AA-clean; white-on-primary is ~3.1:1). The only thing worth capturing would be a dedicated *accessibility / high-contrast theme* if app.peregrine.io has one (unlikely); if it does, run the capture script in that mode and use it as the wcag source.

---

## 7. Build & verify
```powershell
# net10 compiles SCSS; build it first (or Debug first) to avoid the MSB3371 sass.stamp gotcha
dotnet build Radzen.Blazor/Radzen.Blazor.csproj -c Release
dotnet run --project RadzenBlazorDemos.Server   # Debug only; https://localhost:5001
```
- Verify both: `https://localhost:5001/dashboard?theme=peregrine` and `?theme=peregrine-dark` (add `&wcag=true` to check the wcag variants). **Hard-refresh** (CSS href is versioned by assembly version, not content).
- `wwwroot/css/` is a gitignored build artifact.
- Theme already registered in `ThemeService.cs` (`Embedded`); confirm it's in `Themes.All` and that light/dark pairing exists in `RadzenAppearanceToggle.razor.cs` (per CLAUDE.md these were "not yet" done — verify).
- Sanity-check pages: `/dashboard`, plus a DataGrid-heavy and a form-heavy demo for density.

## 8. Known gaps / do NOT chase
- **Per-component pixel values** (button/input/grid padding, heights, per-component radius): not in the captures and not capturable (hashed styled-components). Rely on foundation inheritance + the 14px root + visual QA. BRAND-SPEC's Notion "UI Kit Tokens" (radius scale, fontSize 8–17, size scale, dividerContrast .08/.12/.16) are the documented product sizing reference if needed.
- **h3–h6**: interpolated (§4.6), not measured.
- The bare `button` anchor (600px pill radius) is an outlier — ignore; use the 3px decision.
- Don't reintroduce Blueprint's gray scale — neutrals come from §4.3.

## 9. References
- Captures: `docs/peregrine-theme/captures/` (12 files, raw CSS stripped — see its `README.md`). Originals were generated to `~/Downloads`.
- `docs/peregrine-theme/BRAND-SPEC.md` — "Product design system" section corroborates these values (note: its overall *strategy* line is superseded — see top of this doc).
- `docs/peregrine-theme/THEME-ARCHITECTURE.md` — how Radzen theming works.
- Memory: `peregrine-app-design-system` (architecture correction).
