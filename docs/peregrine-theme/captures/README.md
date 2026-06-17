# Product CSS captures (reference)

Console-script captures of **app.peregrine.io** computed styles + design tokens, used to derive the `peregrine` Radzen theme. The decoded values and the `$rz-*` mapping live in **`../peregrine-scss-plan.md`** (§2 is self-sufficient if these files are ever removed); these raw files are the backing evidence.

## Files (12) — captured 2026-06-16
`peregrine-capture-<page>-<mode>.json`, `<mode>` = `light` | `dark`:
- `peregrine-capture-{light,dark}.json` — the `/home` landing surface.
- `peregrine-capture-bolt-gw-test-org-{pipelines,instances,ontology,properties,manifest}-{light,dark}.json` — real app views.

## What each file contains
- `meta` — page, detected mode, sample element, counts.
- `customProperties` — the resolved `--theme-*` token layer (the product's design system).
- `tokenRules` — the source `.theme-light` / `.theme-dark` declaration blocks (both modes appear in every file).
- `anchors` — computed styles of probed selectors.
- `fonts` — loaded FontFaces (Inter, IBM Plex Mono).
- `stylesheets` — inventory only (`href` / `bytes` / `ruleCount`). **Raw CSS text was stripped** to keep the repo light; the originals (with full embedded CSS) were generated to `~/Downloads`.

## Key findings (full analysis in the plan doc)
- The `--theme-*` token layer is **byte-identical across all 12 captures** (0 value drift) — it's a global design system, so any one file's tokens represent the product.
- The app is **not** Blueprint-classed; components are styled-components (hashed classes) + atomic utilities, so **only base HTML anchors matched** (`html/body/p/a/h1/h2/label/button/input`). Per-component pixel values are therefore not in here and not capturable — see `peregrine-scss-plan.md` §8.
