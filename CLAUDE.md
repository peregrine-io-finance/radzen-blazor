# Peregrine fork of radzen-blazor

Peregrine Technologies' fork of [radzenhq/radzen-blazor](https://github.com/radzenhq/radzen-blazor) (110+ MIT Blazor components). The fork exists to carry the **`peregrine` / `peregrine-dark` theme family** (Peregrine brand) plus minimal build fixes as a small patch stack on top of upstream release tags.

## Fork management

- `master` = upstream release tag + peregrine patches. **Never merge upstream — rebase**: `git fetch upstream` → create `backup/master-pre-<tag>-rebase` → `git rebase v<tag>` → verify build → `git push --force-with-lease origin master`. Currently based on **v11.0.5**.
- **After a rebase, audit the theme for upstream drift.** A new upstream tag can add or restructure components whose shared partials reference new `$rz-*` / `$component-*` variables the peregrine themes don't set — those render with upstream (Material) defaults until themed. Diff the upstream variable surface against what the peregrine `*.scss` files set, give any new variables product-aligned values (all four non-wcag files in lockstep — see Theming quick facts; regenerate the wcag pair if the palette changed), then visual-verify on the demo site (`/dashboard`, `?theme=peregrine`).
- Remotes: `origin` = peregrine-io-finance/radzen-blazor, `upstream` = radzenhq/radzen-blazor.
- Release tags: `v<upstream>+peregrine.<n>` (e.g. `v11.0.5+peregrine.1`); counter resets when rebasing onto a newer upstream.
- Keep the patch stack rebase-friendly: prefer additive changes (new files, flag-guarded blocks) over editing upstream files; isolate shared-file edits in their own commits.
- Conventional commits: `feat:` / `fix:` / `chore:` / `docs:`, imperative, < 72 chars.

## Build

```powershell
dotnet build Radzen.Blazor/Radzen.Blazor.csproj -c Release   # all TFMs: net8/9/10
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
- Theme registration lives in `ThemeService.cs` (`Embedded` switch + `Themes.All` with preview metadata) and `RadzenAppearanceToggle.razor.cs` (light/dark pairing) — both done for peregrine.
- There is no CSS-level test coverage; verification is visual via the demo site.
