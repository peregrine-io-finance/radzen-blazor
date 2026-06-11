# Radzen Theming Architecture — Reference

How theming works in this fork, end to end. Everything here was verified against the source at upstream v10.4.7 + peregrine scaffold (commit `5fc5fddb`). File paths are relative to the repo root.

## 1. Theme composition

Each theme family has **6 files** in `Radzen.Blazor/themes/`:

| File | Role |
|---|---|
| `{name}.scss` | Standalone full sheet (styles global elements too) |
| `{name}-base.scss` | Same + `$base: true` — **this is the file the runtime actually loads** |
| `{name}-wcag.scss` | Hand-written flat `--rz-*` color overrides, loaded as a *second* stylesheet |
| `{name}-dark.scss`, `{name}-dark-base.scss`, `{name}-dark-wcag.scss` | Dark trio, same structure |

The entry and `-base` files are **near-duplicate ~1,400-line files**, not an import chain. Every edit to theme tokens must be made consistently in all four non-wcag files (`peregrine.scss`, `peregrine-base.scss`, `peregrine-dark.scss`, `peregrine-dark-base.scss`). Known intentional drift between entry and `-base`: the `-base` file adds `$base: true` and a `// Gantt` rule (`.rz-gantt .rz-data-grid { --rz-grid-cell-padding: 0 1rem; }`).

Import chain inside each entry file:

```
peregrine.scss
├─ @import 'variables'   → themes/_variables.scss   (flags: $material/$fluent/$standard/$theme-dark/$base, all false !default)
├─ @import 'mixins'      → themes/_mixins.scss      (rz-ripple, filled/outlined/text shade generators, hover-state)
├─ $material: true; $theme-name: peregrine;          (-base adds $base: true; -dark adds $theme-dark: true)
├─ ~714 token definitions (!default) + Material structural CSS (ripple includes, calendar circles, …)
├─ @import 'fonts'       → themes/_fonts.scss
└─ @import 'components'  → themes/_components.scss
       ├─ 87 partials: themes/components/blazor/_*.scss
       └─ @import 'css-variables' → themes/_css-variables.scss  (LAST — emits the :root { --rz-* } block)
```

**`$base: true`** suppresses global element styling (`h1–h6`, `body`, `p`, `label`, `::selection`, `:root font-size`, body scrollbars) via guards in `components/blazor/_typography.scss` and `_scrollbar.scss`, so the stylesheet can be embedded without restyling the host page.

### The three-layer variable mechanism

1. Each partial declares `$component-token: var(--rz-something) !default` at the top — the compile-time hook themes override.
2. `themes/_css-variables.scss` (1,261 lines) flattens every token into `:root { --rz-component-token: …; }`. Compiled `peregrine-base.css` contains ~2,172 `--rz-*` declarations (1,646 unique).
3. Rule bodies consume **only** `var(--rz-*)` — 3,005 usages across the partials. Runtime overrides in plain CSS therefore work without recompiling.

## 2. Token surface (what the peregrine files define)

`peregrine-base.scss` holds **714 SCSS variable declarations**. The load-bearing ones:

**Core palette** (light values, currently Material's — these are what the brand implementation replaces):

```scss
$rz-white: #ffffff;  $rz-black: #000000;
$rz-base: #eeeeee;  $rz-primary: #4340D2;  $rz-secondary: #e31c65;
$rz-info: #2196f3;  $rz-success: #4caf50;  $rz-warning: #ff9800;  $rz-danger: #f44336;
```

- **Neutral scale**: `$rz-base-50…900` + `-light/-lighter/-dark/-darker` (14 vars). Dark theme inverts and darkens this ramp.
- **Derived shades**: `$rz-{hue}-light: mix($rz-white, $hue, 20%)`, `-lighter: rgba($hue, .12–.20)`, `-dark/-darker: mix($rz-black, …)` — defined per hue; keep the derivation pattern and only the base hues need changing, unless the brand needs explicit shades.
- **`$rz-theme-colors-map`** (106 entries) generates `--rz-{token}` plus auto-derived `--rz-border-{token}` and `--rz-outline-{token}` for every color, and contains the contrast pairs (`on-primary`, `on-primary-lighter`, …) that keep text legible on tinted surfaces.
- **Chart series**: `$rz-series-1…24` (currently Material purples).
- **Theme constants**: `$rz-border-radius: 4px`, `$rz-root-font-size: 16px`, `$rz-body-line-height: 1.5`, `$rz-text-font-family: Roboto, sans-serif`, `$rz-body-background-color: var(--rz-base-100)`, outline width/offset/color.
- **Semantic text**: `$rz-text-title-color/-color/-secondary-color/-tertiary-color/-disabled-color/-contrast-color` (point at the neutral ramp).
- **Shadows**: `$rz-shadow-0…10` — Material elevation stacks; per-theme, define light and dark scales.
- **Typography `$text` map**: 21 styles (`display-h1..h6`, `h1..h6`, `subtitle1/2`, `body1/2`, `button`, `caption`, `overline`) → `--rz-text-{style}-{prop}` + `.rz-text-{style}` classes. The map has **no per-style font-family key** — font-family is applied globally (`components/blazor/_typography.scss:79`). A heading-vs-body font split requires adding `font-family` keys to the heading entries or introducing a `--rz-heading-font-family` token.
- Remaining ~600 vars are per-component (Grid 56, Scheduler 52, DatePicker 42, PanelMenu 38, FormField 27, Pager 25, Dropdown 24, Tabs 24, Menu 23, Dialog 18, …) — mostly pointers at the palette/semantic tokens, so they follow automatically; only re-point them where the design intends a different relationship.

### Light vs dark deltas (from the Material scaffold; pattern to replicate)

The dark files differ from light in: the core palette swap (`$rz-primary: #bb86fc`, `$rz-secondary: #01a299`, `$rz-base: #383838`), the inverted neutral ramp (`base-50: #e0e0e0` … `base-900: #121212`), `$rz-body-background-color: var(--rz-base-900)`, flipped semantic text colors, `"on-base": $rz-white`, `$rz-base-background-color: var(--rz-base-800)` (light inherits `var(--rz-white)`), and ~150 re-pointed component tokens (header/menu surfaces, border tokens stepped up, dialog/notification surfaces, white-alpha ripples and scrollbars). Some tokens exist **only** in the dark files (chip background, scheduler weekend, skeleton, timeline, splitter colors) and some only in light (button focus outline, sidebar color, tab focus colors) — diff the pairs before assuming symmetry.

Known inherited typos: `$checkbox-checked-disabled-border: var(--rz-border-300)` in the dark files should be `var(--rz-border-base-300)` (inherited from material-dark); the light files' `--rz-base-backgorund-color` typo in `$selectbar-background-color` was fixed only in dark.

## 3. WCAG variants

`peregrine-wcag.scss` / `peregrine-dark-wcag.scss` are **85-line plain-CSS files** (no SCSS imports): a single `:root, .rz-peregrine { … }` rule re-declaring only the 82 non-series color custom properties with pre-resolved higher-contrast literals. They compile standalone and load as a second `<link>` after the theme (`ThemeService.WcagHref`, toggled by `SetWcag(true)` / `<RadzenTheme Wcag="true">`). **They still contain Material's WCAG palette** and must be hand-regenerated from accessible variants of the Peregrine palette (target ≥ 4.5:1 contrast for text-bearing pairs; Material's versions darken secondary/info/success/danger and flip `on-warning` to black).

## 4. Runtime surface

- **`Radzen.Blazor/ThemeService.cs`** — `Themes.All` registry (14 entries; **peregrine is NOT in it yet**), `Theme` metadata record (preview colors + radii + `Premium` flag), `ThemeService.SetTheme()` hot-swaps `<link>` hrefs via `Radzen.setTheme` JS (no reload). `Href => "{Path}/{Theme}-base.css?v={assemblyVersion}"`. The `Embedded` switch (L487–502) already includes `"peregrine"`/`"peregrine-dark"` → CSS resolves to `_content/Radzen.Blazor/css/…`.
- **`RadzenTheme.razor(.cs)`** — renders the theme `<link>`, optional wcag `<link>`, and preloads `MaterialSymbolsOutlined.woff2` (only the icon font is preloaded).
- **`RadzenAppearanceToggle.razor.cs`** — light/dark toggle with **hardcoded pairing switches** (`CurrentLightTheme`/`CurrentDarkTheme`, L54–76). Peregrine is absent → the toggle is a no-op for peregrine until the maps are extended (or consumers pass `LightTheme="peregrine" DarkTheme="peregrine-dark"`).
- **Persistence**: `CookieThemeService`, `QueryStringThemeService` (`?theme=`, `?wcag=`, `?rtl=`) — name-agnostic, nothing to change. `?theme=peregrine` already works because `Embedded` resolves the CSS path without consulting `Themes.All`.
- **Complete list of hardcoded theme-name lists** in the repo: `ThemeService.Embedded` (done), `Themes.All` (todo), `RadzenAppearanceToggle` pairing maps (todo), demo `<RadzenTheme Theme="material3" />` defaults, SCSS conditionals (`$material`, `$fluent`, `$theme-name == material3` in `_card.scss`/`_grid.scss:843`/`_fonts.scss`). `Radzen.Blazor.js` has none.

### Premium themes

`material3`, `material3-dark`, `fluent`, `fluent-dark` are `Premium = true` and their SCSS is **not in this repo** — only precompiled CSS checked into `RadzenBlazorDemos/wwwroot/css` for demo purposes. Premium themes resolve to the app's own `css/` folder (not `_content/`). Peregrine is free/embedded; nothing premium-related applies to it.

## 5. Fonts

`themes/_fonts.scss` (35 lines), `$fonts-path: '../fonts'` → resolves at runtime to `_content/Radzen.Blazor/fonts/`. Current behavior:

- Always: `@font-face 'Material Symbols'` ← `MaterialSymbolsOutlined.woff2` (variable, 100–700) — the icon font.
- `@if $material == false and $fluent == false`: Source Sans 3 variable fonts (default/standard/humanistic/software themes).
- `@if $material == true`: **Roboto ← `RobotoFlex.woff2`, embedded in `wwwroot/fonts/`** (not a CDN load). Peregrine currently has `$material: true`, so it ships Roboto today.

`wwwroot/fonts/` ships automatically as static web assets (default globbing — no csproj item needed). Existing `@font-face` blocks use variable fonts (`format('woff2-variations')`); Protocol/Kale are static weights and need one `@font-face` block per weight/style with `font-display: swap`.

## 6. Build pipeline

Multi-targets `net7.0;net8.0;net9.0;net10.0`. The Sass/JS pipeline is **net10.0-only** (`Radzen.Blazor.csproj` L63–94, tasks from `Radzen.MSBuild` 0.0.6):

1. `CompileSass` (BeforeTargets=MoveCss): compiles every non-partial `themes/*.scss` → sibling `.css` (compressed), incremental via `obj/.../sass.stamp`.
2. `MoveCss` (BeforeTargets=BeforeBuild): moves `themes/*.css` → `wwwroot/css/` and re-adds as Content.
3. `MinifyJs`: `Radzen.Blazor.js` → `.min.js`.

- **`wwwroot/css/` is gitignored** — compiled CSS exists only as a build artifact. Build net10.0 first so older TFMs pick up the CSS.
- **Gotcha — clean Release single-TFM build fails**: `Touch sass.stamp` runs before `PrepareForBuild` creates `obj/Release/net10.0/`, failing with MSB3371. Workaround: pre-create the directory, or build Debug first. Permanent fix candidate: add `<MakeDir Directories="$(IntermediateOutputPath)"/>` inside the `CompileSass` target.
- After SCSS edits: rebuild Radzen.Blazor (net10.0), then **hard-refresh** the browser — the CSS href is versioned by assembly version, not content hash.

## 7. Verifying a theme visually

```powershell
cd C:\Users\GarrettWomack\source\repos\Peregrine.Finance\radzen-blazor
dotnet run --project RadzenBlazorDemos.Server
# https://localhost:5001/?theme=peregrine   (&wcag=true, &rtl=true to test those modes)
```

- Use **`RadzenBlazorDemos.Server`** (Blazor Server — fast). `RadzenBlazorDemos.Host` is the production blazor.radzen.com host (WASM, slow builds).
- **Run in Debug.** In Release, `RadzenBlazorDemos.csproj` swaps the Radzen.Blazor ProjectReference for the published NuGet package — local theme changes would silently not be used.
- Default demo theme is `material3`; switch via `?theme=peregrine` or the "Demos Configuration" sidebar (peregrine appears in the sidebar/theme pages only once added to `Themes.All`).
- No DB needed (EF Core InMemory, self-seeding Northwind). Prereq: .NET 10 SDK, `dotnet dev-certs https --trust` once.
- `/dashboard` is the best single QA page (cards, grid selection, badges, tabs, chart palettes together). The cross-cutting surfaces to check in both modes: selection states, the severity × variant × shade matrix (buttons/alerts/badges/chips), every popup/overlay (`--rz-panel-*`), density, elevation.

## 8. Test coverage reality

There is **no test coverage of the SCSS/compiled-CSS layer**: `ThemeTests.cs` only asserts `<link>` URLs; 984 `rz-*` class-name assertions across bUnit tests pin the markup↔SCSS class contract but no style values. Verification today is purely visual. Cheap additions if regression protection is wanted: golden-file diffs of compiled CSS, and a token-contract test asserting the `--rz-*` set in compiled `peregrine-base.css`.

## 9. Component style layer facts

- 87 partials in `themes/components/blazor/`, aggregated by `themes/_components.scss` (flat ordered imports, `css-variables` last).
- Largest theming surfaces: `_grid.scss` (100 vars / 1,334 lines), `_scheduler.scss` (69), `_datepicker.scss` (58), `_utilities.scss` (55 — also defines the shared maps and utility classes), `_panel-menu.scss` (53).
- **Hardcoded colors that bypass theming** (the only real gaps): `_chart.scss:43–71` and `_sankey.scss:71–119` palette/mono/divergent color schemes (~57 raw hex; only the `pastel` scheme routes through `--rz-series-*`), chart tooltip shadow (`_chart.scss:263`), gantt shadows (`_gantt.scss:211,229,250`), frozen-column edge shadows (`_grid.scss:1123,1127`), colorpicker inset borders (legitimately fixed color-science gradients otherwise).
