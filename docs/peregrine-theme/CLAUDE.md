# CLAUDE.md — Peregrine theme retheme (TASK BRIEF — deletable)

**This is the #1 priority in this repo right now.** Retheme the Radzen `peregrine` / `peregrine-dark` theme so apps built on this fork look like the **app.peregrine.io** product UI (light + dark). Prep is complete — **this is now an implementation task.**

> _Delete this file (and revert the priority block in the repo-root `CLAUDE.md`) when the retheme ships or focus changes._

## Authoritative spec
**`peregrine-scss-plan.md`** (this folder) is the source of truth — read it fully before editing SCSS. It has the decoded product tokens (both modes), the exact `$rz-*` mapping, derivation rules, and the WCAG/build/verify steps. Backing data: `captures/`. Superseded context (do not follow their *strategy*): `IMPLEMENTATION-PLAN.md`, `BRAND-SPEC.md` (its product *values* are still valid).

## Locked decisions — do not re-litigate
- **Full product-match** — drop the old brand direction (Bright Orange, Protocol/Kale fonts, navy dark, square corners, giant display headings).
- Root **14px**; fonts **Inter** (UI) + **IBM Plex Mono** (mono/caption); radius **3px**.
- Primary **#678EF1**; secondary neutral **#99A1A8**; success/warning/danger **#1BA572 / #D99919 / #ED5F74**; links **#4168CB** (light) / **#85ADF7** (dark).
- Surfaces: light canvas **#EDF0F2** / card **#FFFFFF**; dark canvas **#1A1B1C** / card **#1F2021**. Text = alpha tiers 100/66/50/33%.
- Headings **Inter 700**, modest sizes (h1 40 → h6 14px), line-height 1.4. Shadows = product ring+drop. Chart series = product 7-hue palette.

## Implementation order
1. **Edit the 4 non-wcag files in lockstep**: `peregrine.scss` + `peregrine-base.scss` (light), `peregrine-dark.scss` + `peregrine-dark-base.scss` (dark). Set the foundation per plan §4; component vars inherit.
2. **Regenerate** `peregrine-wcag.scss` / `peregrine-dark-wcag.scss` — hand-authored flat 82-key `--rz-*` files, AA ≥ 4.5:1 (plan §6).
3. **Register**: `Radzen.Blazor/ThemeService.cs` → `Themes.All` entry + preview metadata; `RadzenAppearanceToggle.razor.cs` → light/dark pairing (both "not yet" done). Add bUnit cases.
4. **Build & verify** (below). Iterate the proposed ramps/type scale (plan §4.3/§4.6) visually.

## Build & verify
- `dotnet build Radzen.Blazor/Radzen.Blazor.csproj -c Release` — SCSS compiles in **net10.0 only**; build net10 first (or Debug first) to dodge the MSB3371 `sass.stamp` gotcha. `wwwroot/css/` is gitignored.
- `dotnet run --project RadzenBlazorDemos.Server` (**Debug only**) → `https://localhost:5001/dashboard?theme=peregrine` and `?theme=peregrine-dark` (+`&wcag=true`). **Hard-refresh** after SCSS edits. QA pages: `/dashboard`, `/datagrid`, `/button`, `/colors`, `/typography` — each in light, dark, wcag.

## Guardrails
- **Don't chase per-component pixel values** — not capturable (hashed styled-components); derive from the foundation + visual QA (plan §8).
- Don't reintroduce Blueprint's gray scale or the brand orange/Protocol direction.
- Keep the fork patch-stack rebase-friendly (root `CLAUDE.md`): additive where possible; isolate shared-partial edits in their own commit. Conventional commits; build between steps.
- Verify non-peregrine themes stay byte-identical if you touch shared partials (golden-CSS diff).
