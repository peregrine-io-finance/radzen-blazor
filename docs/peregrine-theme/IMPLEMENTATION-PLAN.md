# Peregrine Theme — Implementation Plan

> **⚠ Strategy changed 2026-06-16 — full product-match.** The implementation source of truth is now
> **[`peregrine-scss-plan.md`](peregrine-scss-plan.md)** — decoded product `--theme-*` tokens (both modes,
> verified across 12 captures) + the exact `$rz-*` mapping. It **supersedes the "hybrid" decision and several
> rows below**: primary `#FF6201` → **`#678EF1`**; fonts Protocol/Kale → **Inter + IBM Plex Mono**; radius
> `0` → **3px**; root `16px` → **14px**; dark navy `#172935` → **product cool-grey `#1A1B1C`/`#1F2021`**;
> headings 400-weight `clamp()` → **700, modest sizes**; chart series brand → **product palette**. The
> **phase structure (0–7) below still applies as process** — take the *values* from the new plan.

Goal: implement the Peregrine Technologies brand into the `peregrine` / `peregrine-dark` theme profiles (plus WCAG variants) of this Radzen Blazor fork. The scaffold (commit `5fc5fddb`) renders identically to Material today — every Material value is a placeholder to replace.

Read first: `THEME-ARCHITECTURE.md` (how theming works here), `BRAND-SPEC.md` (the design inputs and their status).

## Decisions already made (2026-06-10, Garrett)

| Decision | Outcome |
|---|---|
| Design source of truth | **Hybrid**: 2026 brand identity (Protocol/Kale fonts, Brand Guidelines PDF palette, peregrine.io) for fonts and primary/secondary colors; product UI Kit tokens for UI semantics the brand guide doesn't cover (dark-mode surfaces, state colors, density) |
| Dark mode | In scope; to be **designed**, complementary to light mode (no designed dark brand palette exists) |
| Fonts | **Embed in the library** (`wwwroot/fonts/` + `@font-face`), pending Protocol license verification |
| WCAG variants | **In scope** — regenerate from accessible variants of the brand palette |
| Chart series palette | **In scope** — derive the 24 series from brand accents; tokenize hardcoded chart/sankey palettes |
| Theme picker registration | **In scope** — `Themes.All` entry + `RadzenAppearanceToggle` pairing |
| Fork update model | Rebase peregrine patches onto upstream release tags; version `v<upstream>+peregrine.<n>` (currently on v10.4.7) |

## Open items (resolve before or during Phase 1)

1. **Brand palette — RESOLVED.** The Brand Guidelines PDF v1 was extracted (2026-06-10): Bright Orange `#FF6201` accent, Blue 0–3 range (`#172935`/`#264757`/`#9DC9DA`/`#E2EFFF`), Black/Off-White foundation, gray + olive secondary ramps. See BRAND-SPEC's "Consolidated Radzen token mapping" for working Phase 1/2 values and the four remaining design calls (notably: `$rz-secondary` blues-vs-teal, and whether Bright Orange as `$rz-primary` spreads orange wider than the brand's "use sparingly" rule intends). Dark mode must anchor on Blue 0/Blue 1 navy — the PDF forbids black/dark-gray backgrounds.
2. **Protocol web-embedding license** — softened: peregrine.io itself publicly serves the exact APK Protocol OTFs via `@font-face` (URLs in BRAND-SPEC), so web-serving has precedent; shipping them inside this package's static web assets and/or converting to WOFF2 should still be confirmed with #brand-design. **Owner: Garrett.** Fallback that needs no new clearance question: serve the same OTFs the site serves (`format('opentype')`).
3. **Font roles** — resolved by the live site: Protocol 400 for body AND headings (headings differ by size/leading, not weight), Protocol 700 for buttons, Kale Sans Mono 400 uppercase for labels/overline ("intel" style). Confirm against PDF.
4. **Kale has Regular only** — consistent with the site (labels only, never bold). Restrict Kale to overline/caption surfaces; no faux-bold.
5. **Semantic state colors** (success/warning/danger) — no marketing-site equivalents; per hybrid strategy take from product palette (green `#1BA572`, yellow `#D99919`, red `#ED5F74`; info = Trust `#0076a0`), then design `on-*` contrast pairs. Verify contrast at Radzen's usage sites (filled buttons, alerts, badges).
6. **Elevation & radius** — radius resolved: **sharp corners are a brand trait** (`border-radius: 0` across peregrine.io) → plan for `$rz-border-radius: 0` (verify component edge cases: pills, badges, switches, FAB may want explicit non-zero values). Elevation: site uses light, simple shadows — flatten Radzen's Material stacks toward subtle values rather than keeping full elevation drama.
7. **Icon font** — keep Material Symbols (no brand iconography exists). Revisit only if the brand ships icons.
8. **Root font size** — site body is 14→16px responsive; product app is 13px; Radzen root is 16px. Default: keep `$rz-root-font-size: 16px` (Radzen's rem-based sizing assumes it) and tune per-style sizes in the `$text` map instead.

## Phases

Each phase is a separate commit (conventional prefixes). Build + spot-check between phases.

### Phase 0 — Build hygiene (chore)
- Add `<MakeDir Directories="$(IntermediateOutputPath)"/>` before `Touch` in the `CompileSass` target (fixes clean Release single-TFM builds, MSB3371).
- Optional drive-by fixes in the peregrine files only: `$checkbox-checked-disabled-border: var(--rz-border-300)` → `var(--rz-border-base-300)` (dark pair); `--rz-base-backgorund-color` typo in `$selectbar-background-color` (light pair).

### Phase 1 — Light palette (feat)
Files: `peregrine.scss` + `peregrine-base.scss` (keep the pair in lockstep; only intentional diffs are `$base: true` and the Gantt rule).
- Replace the core palette (`$rz-primary`, `$rz-secondary`, `$rz-base`, info/success/warning/danger) and the neutral ramp (`$rz-base-50…900`, `-light/-lighter/-dark/-darker`) per `BRAND-SPEC.md`.
- Keep the `mix()`/`rgba()` shade-derivation pattern unless the brand defines explicit shades.
- Update contrast pairs in `$rz-theme-colors-map` (`on-primary`, `on-secondary`, …) — verify ≥ 4.5:1 for text-bearing pairs.
- Update `$rz-body-background-color`, semantic text colors, borders if the neutral ramp shifts their meaning.
- Decide `$material: true` handling: introduce `$peregrine: true` flag (see Phase 3) but keep Material structural CSS (ripple, calendar circles) unless the design says otherwise.

### Phase 2 — Dark palette (feat)
Files: `peregrine-dark.scss` + `peregrine-dark-base.scss`.
- Design the dark ramp complementary to light: **navy-anchored, not gray** — body on Blue 0 `#172935`, surfaces stepping through Blue 1 `#264757` and darkened blue tints (the PDF forbids black/dark-gray backgrounds; the live site's `data-theme=modern` is the precedent). Product app dark `--theme-*` values in BRAND-SPEC remain the reference for text-tier relationships.
- Replicate the Material dark-delta pattern (~150 re-pointed component tokens — see THEME-ARCHITECTURE §2); don't assume light/dark symmetry, diff the pairs.
- Verify `on-*` contrast pairs in dark.

### Phase 3 — Typography & fonts (feat)
- Convert Protocol OTFs → WOFF2 (subset to latin if size matters); Kale Regular WOFF2 already exists. **Blocked on license check.**
- Drop files into `Radzen.Blazor/wwwroot/fonts/` (ships automatically).
- Add `$peregrine: false !default` to `themes/_variables.scss`; set `$peregrine: true` in all four peregrine non-wcag files.
- In `_fonts.scss`: add `@if $peregrine == true` block with static `@font-face` per weight/style (`font-display: swap`); change the Roboto guard to `@if $material == true and $peregrine == false` so peregrine CSS stops shipping RobotoFlex.
- Set `$rz-text-font-family` (e.g. `'Protocol', Roboto, sans-serif` — final stack per BRAND-SPEC). If headings use a different font, add `font-family` keys to the `$text` map heading entries or introduce `--rz-heading-font-family`.
- Mono surfaces (code blocks in Markdown/Editor): set per font-role decision (Kale Sans Mono vs IBM Plex Mono).

### Phase 4 — Registration (feat)
- `ThemeService.cs`: add `Theme { Text = "Peregrine", Value = "peregrine", Premium = false, … }` + dark twin to `Themes.All`, with preview metadata (Primary, Secondary, Base, Content, Selection, SelectionText, TitleText, ContentText, ButtonRadius, CardRadius, SeriesA/B/C) — the demo sidebar draws the SVG thumbnail from these.
- `RadzenAppearanceToggle.razor.cs`: add `"peregrine"`/`"peregrine-dark"` to both pairing switches.
- bUnit tests: add cases to `ThemeTests.cs`/`AppearanceToggleTests.cs` mirroring existing patterns.

### Phase 5 — Charts & series (feat)
- Define `$rz-series-1…24` from the brand/product accent set (7 accents → a 24-ramp via lightness steps; document the derivation in BRAND-SPEC).
- Tokenize the hardcoded `palette`/`mono`/`divergent` schemes in `_chart.scss:43–71` and `_sankey.scss:71–119` (route through `--rz-series-*` like `pastel` does) — this touches shared partials, so verify other themes still compile identically (golden-CSS diff, Phase 7).
- Optional: tokenize gantt/grid/chart shadow literals.

### Phase 6 — WCAG variants (feat)
- Regenerate `peregrine-wcag.scss` / `peregrine-dark-wcag.scss`: keep the 82-property flat structure and `.rz-peregrine` selectors; compute accessible variants of the Phase 1/2 palette (pattern: darken secondary/info/success/danger toward AAA, flip `on-warning` to black if warning stays bright).
- Verify with a contrast checker; test via `?theme=peregrine&wcag=true`.

### Phase 7 — Verification & ship
- Full build: `dotnet build Radzen.Blazor/Radzen.Blazor.csproj -c Release` (all TFMs; net10.0 regenerates CSS).
- `dotnet test Radzen.Blazor.Tests`.
- Visual matrix on `RadzenBlazorDemos.Server` (**Debug only** — Release swaps to the NuGet package): `/dashboard`, `/datagrid` (filters/selection/frozen), `/button`, `/alert` + `/badge` (severity matrix), `/dropdown` + dialogs/tooltips (popups), `/scheduler`, `/chart`, `/typography`, `/colors` — each in light, dark, and wcag.
- Optional but recommended: golden-file diff of all compiled `wwwroot/css/*.css` against the pre-change build to prove non-peregrine themes are byte-identical (mandatory if Phase 5 touched shared partials).
- Tag `v10.4.7+peregrine.<n>`; push.

## Working rules for this fork

- Keep the patch stack small and rebase-friendly: prefer additive changes (new files, new flag-guarded blocks) over edits to shared partials; when a shared partial must change, isolate it in its own commit with a note on rebase risk.
- `master` is the patch stack on top of upstream tags. Update procedure: fetch upstream → `git rebase v<new-tag>` → verify build → `git push --force-with-lease` (create a `backup/master-pre-<tag>-rebase` branch first).
- Conventional commits (`feat:`/`fix:`/`chore:`/`docs:`); subjects < 72 chars, imperative.
