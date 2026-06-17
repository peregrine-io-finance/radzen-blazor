# Peregrine fork of radzen-blazor

Peregrine Technologies' fork of [radzenhq/radzen-blazor](https://github.com/radzenhq/radzen-blazor) (110+ MIT Blazor components). The fork exists to carry the **`peregrine` / `peregrine-dark` theme family** (Peregrine brand) plus minimal build fixes as a small patch stack on top of upstream release tags.

## Active work — CURRENT PRIORITY #1: Peregrine theme = product-match retheme

Retheming `peregrine` / `peregrine-dark` to match the **app.peregrine.io** product UI (light + dark) is the **top priority** in this repo right now. Prep and design decisions are **done** — the next step is implementing the SCSS. **Start here, in order:**

1. **`docs/peregrine-theme/CLAUDE.md`** — task brief: locked decisions + implementation order. Read first.
2. **`docs/peregrine-theme/peregrine-scss-plan.md`** — authoritative spec: decoded product tokens (both modes), exact `$rz-*` mapping, derivations, WCAG/build/verify.
3. `docs/peregrine-theme/captures/` — backing product-CSS captures (reference).

Background (strategy superseded 2026-06-16 — full product-match, not the old "hybrid"): `IMPLEMENTATION-PLAN.md` (phase process), `BRAND-SPEC.md` (product values valid; strategy superseded), `THEME-ARCHITECTURE.md` (read before touching SCSS).

> _Spin-down: delete `docs/peregrine-theme/CLAUDE.md` and revert this section when the retheme ships or focus changes._

## Fork management

- `master` = upstream release tag + peregrine patches. **Never merge upstream — rebase**: `git fetch upstream` → create `backup/master-pre-<tag>-rebase` → `git rebase v<tag>` → verify build → `git push --force-with-lease origin master`. Currently based on **v10.4.7**.
- Remotes: `origin` = peregrine-io-finance/radzen-blazor, `upstream` = radzenhq/radzen-blazor.
- Release tags: `v<upstream>+peregrine.<n>` (e.g. `v10.4.7+peregrine.1`); counter resets when rebasing onto a newer upstream.
- Keep the patch stack rebase-friendly: prefer additive changes (new files, flag-guarded blocks) over editing upstream files; isolate shared-file edits in their own commits.
- Conventional commits: `feat:` / `fix:` / `chore:` / `docs:`, imperative, < 72 chars.

## Build

```powershell
dotnet build Radzen.Blazor/Radzen.Blazor.csproj -c Release   # all TFMs: net7/8/9/10
dotnet test Radzen.Blazor.Tests                               # xUnit + bUnit, ~100 classes
```

- SCSS compiles **only in the net10.0 target** (Radzen.MSBuild `SassCompile` → `themes/*.css` → moved to `wwwroot/css/`). Build net10.0 before/with older TFMs. `wwwroot/css/` is **gitignored** — compiled CSS is a build artifact.
- **Gotcha**: clean Release single-TFM build fails with MSB3371 (`sass.stamp`) because `obj/Release/net10.0/` doesn't exist when `CompileSass` runs. Pre-create the directory or build Debug first (permanent fix planned: `<MakeDir>` in the target).
- After SCSS edits: rebuild, then **hard-refresh** the browser (CSS href is versioned by assembly version, not content).

## Run the demo site (visual verification)

```powershell
dotnet run --project RadzenBlazorDemos.Server   # https://localhost:5001
```

- **Debug configuration only** — in Release the demos reference the published Radzen.Blazor NuGet package instead of the local project, silently ignoring local changes.
- Open `https://localhost:5001/?theme=peregrine` (add `&wcag=true` / `&rtl=true` for those modes). Default theme is material3.
- Use `RadzenBlazorDemos.Server` (fast, Blazor Server), not `.Host` (the production WASM site). No DB needed.
- Best QA page: `/dashboard`. Theme switcher sidebar lists a theme only once it's in `Themes.All` (`Radzen.Blazor/ThemeService.cs`).

## Theming quick facts

- A theme = 6 files in `Radzen.Blazor/themes/`: `{name}.scss`, `{name}-base.scss` (what the runtime loads), `{name}-wcag.scss`, + dark trio. Entry and `-base` are near-duplicates — **edit all four non-wcag peregrine files in lockstep**.
- Components consume only `var(--rz-*)` custom properties; themes set ~714 SCSS `$rz-*`/`$component-*` vars that `_css-variables.scss` flattens into `:root`.
- Theme registration lives in `ThemeService.cs` (`Embedded` switch — done for peregrine; `Themes.All` — not yet) and `RadzenAppearanceToggle.razor.cs` (light/dark pairing — not yet).
- There is no CSS-level test coverage; verification is visual via the demo site.
