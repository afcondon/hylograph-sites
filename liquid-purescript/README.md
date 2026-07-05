# liquid-purescript (site)

Documentation / showcase site for **Liquid PureScript** — the Liquid
Haskell-style refinement-type verifier at
[`afc-work/liquid-purescript`](https://github.com/afcondon/liquid-purescript)
(marginalia #246). Built from `liquid-purescript/docs/website-brief.md`.

Static, hand-authored, no build step. Swiss/International style, palette
shared with `signal-box/` so the verification family reads as one house.

## Pages

| File | Section |
|------|---------|
| `index.html` | Overview — hero (paired code/spec/verdict), what refinements buy, honesty constraints, getting started |
| `how-it-works.html` | Pipeline diagram, Resolve/CoreFn, checking-mode semantics, the two bugs worth telling |
| `language.html` | The `.lps` spec language by example — refinements, dependent binders, `assume`, `data`/`measure` |
| `verdicts.html` | The full golden verdict table (`test/golden.txt`), grouped by module, SAFE/UNSAFE/UNSUPPORTED badges |
| `roadmap.html` | Phases 0–4 with dated status; the soundness-trap corpus research-validation story |
| `style.css` | Shared stylesheet |

## Honesty constraints (baked into the copy — keep them)

- Verifies a **fragment** (first-order Int/Boolean/measured-data); everything
  else reports **UNSUPPORTED**, never a silent pass.
- `assume` specs are **trusted, not checked** — said wherever they appear.
- **Phase 3 (higher-order)** and **Phase 4 (type classes, row-poly records)**
  do not exist yet — described as roadmap only.

## Deploy

Not yet wired to a CF Pages project. Manual `wrangler` deploy, matching the
family's non-git-connected sites:

```sh
npx wrangler pages deploy liquid-purescript --project-name liquid-purescript --branch main --commit-dirty=true
```

Then attach a custom domain in the CF dashboard (candidate:
`liquid.hylograph.net`, TBD). Once created, add a row to the top-level
`cloudflare-sites/README.md` table and register the child project under
**Websites #228 → cloudflare-sites #52** in marginalia.
