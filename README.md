# Cloudflare Sites

Monorepo of static sites deployed to Cloudflare Pages. Each subdirectory is the deployable artifact for a separate CF Pages project.

Renamed from `hylograph-sites` on 2026-04-24 to reflect its actual role (it holds all Cloudflare Pages deploys, not just Hylograph-branded ones). GitHub repo is now [`afcondon/afc-cloudflare-sites`](https://github.com/afcondon/afc-cloudflare-sites); local directory kept as `cloudflare-sites` for brevity.

## Sites

| Directory | URL | CF project | Git-connected? | Tracker project |
|-----------|-----|------------|----------------|-----------------|
| `docs/` | hylograph.net | `hylograph-sites` | ✓ (auto-deploys on push) | #230 |
| `blog/` | blog.hylograph.net | `hylograph-blog` | ✓ (auto-deploys on push) | #142 |
| `polyglot/` | polyglot.hylograph.net | `hylograph-polyglot` | ✓ (auto-deploys on push) | #231 |
| `andrewcondon/` | andrewcondon.com (+ andrewcondon.pages.dev) | `andrewcondon` | ✗ (manual wrangler) | #232 |
| `heresiarch/` | heresiarch.com | `heresiarch` | ✗ (manual wrangler) | #233 |
| `signal-box/` | signal-box.hylograph.net | `signal-box` ¹ | TBD ¹ | #234 |
| `widgets/` | widgets.hylograph.net ² | `hylograph-widgets` | ✗ (manual wrangler) | #244 |
| `liquid-purescript/` | liquid-purescript.hylograph.net | `liquid-purescript` | ✗ (`quartermaster publish`) | #247 |
| _(external — `music/harmonia/site/`)_ | harmonia.andrewcondon.com (+ harmonia-c8x.pages.dev) | `harmonia` | ✗ (manual wrangler; source in `purescript-harmonia`, not a subdir here) | — |

¹ signal-box is the Polyglot flagship demo ("Make Illegal States Unrepresentable", static, five pages). Its CF project name and git-connected status are assumed/unconfirmed — verify before relying on them.

² widgets is the **Hylograph Halogen UI** gallery (the `purescript-hylograph-halogen-ui` showcase — fourteen widgets on one controlled contract). Project `hylograph-widgets` created and first-deployed 2026-06-24, live at [hylograph-widgets.pages.dev](https://hylograph-widgets.pages.dev). Custom domain `widgets.hylograph.net` still to be attached in the CF dashboard (Pages → hylograph-widgets → Custom domains).

`andrewcondon/` also hosts the generated **"Currently"** page at `andrewcondon/working/`, a Mondrian grid + per-project report pages pulled from the project tracker. Regenerate with `node working/build.mjs` (tailnet only — see that dir's files), then deploy andrewcondon as below.

## Project tracker structure

In marginalia this repo is **cloudflare-sites (#52)**, a hosting *venue* under the **Websites (#228)** umbrella. Each deployed site above is its own child project (ids in the table). The sibling venue is **GitHub Pages (#229)**, which curates the Pages-served repos via `related` edges rather than owning them.

```
Websites #228
├── cloudflare-sites #52   ← this repo; one child per site (see table)
└── GitHub Pages #229      ← related→ #195 Elements, #69/#70 sigil(+hats), #150 hylograph-demos
```

## Rebuilding

Most subdirs are built artifacts from upstream repos — rebuild upstream, copy into here, commit.

```bash
# In polyglot/purescript-polyglot-site (was purescript-polyglot until 2026-07-30)
make blog                                # Rebuilds blog (site/website is a
                                         #   separate Halogen site — see below)
cd site/hylograph-net && ./build.sh      # Rebuilds docs
cd site/polyglot      && ./build.sh      # Rebuilds the polyglot site (pandoc)

# Then copy to this repo
cp -r site/hylograph-net/public/*  ../../cloudflare-sites/docs/
cp -r blog/public/*                ../../cloudflare-sites/blog/
cp -r site/polyglot/public/*       ../../cloudflare-sites/polyglot/
```

Two corrections vs. what this file used to say: `polyglot/` is built from
**`site/polyglot`** (hand-authored `index.html` + `style.css`, pandoc for the
content pages), not from `site/website` — that is a *different* Halogen site
(the `:3040` demo site). And the relative hops are now `../../` because the
source repo moved one level deeper.

For the three git-connected `hylograph-*` CF projects, a `git push` here is the deploy. For the manual ones:

```bash
npx wrangler pages deploy andrewcondon --project-name andrewcondon
npx wrangler pages deploy heresiarch   --project-name heresiarch
npx wrangler pages deploy signal-box   --project-name signal-box   # CF project name assumed — see ¹ above
npx wrangler pages deploy widgets      --project-name hylograph-widgets --branch main --commit-dirty=true
```

`harmonia` is the odd one out — it deploys the engraved showcase from its **own
repo** (`music/harmonia/site/`), not a subdir here, and is recorded above only
for inventory completeness:

```bash
# from the afc-work root:
npx wrangler pages deploy music/harmonia/site --project-name harmonia --branch main --commit-dirty=true
```

Custom domain `harmonia.andrewcondon.com` is attached in the CF dashboard
(Pages → harmonia → Custom domains); the CNAME is auto-created since
`andrewcondon.com` is on Cloudflare.

`liquid-purescript/` deploys through the infrastructure, not raw wrangler — it's
an `x-bosun.static` site published by **`quartermaster publish`** (ensures the CF
project, stages a clean artifact, deploys, and attaches the custom domain +
CNAME). Its `compose.yml`/`registry.json` in the dir are the declaration, not
served (staging excludes them). See `ShapedSteer/bosun/docs/PUBLISH-A-SITE.md`;
host-prep is `wrangler login` + a `QM_CF_DNS_TOKEN` (Zone:DNS:Edit on hylograph.net).

The `widgets/` artifact is rebuilt from the showcase in `purescript-hylograph-halogen-ui`:

```bash
# In purescript-hylograph-halogen-ui/showcase
spago bundle -p hylograph-halogen-ui-showcase --minify   # → bundle.js (minified)
cp bundle.js index.html hylograph-ui.css sigil.css  ../../cloudflare-sites/widgets/
```

## Canonical record

The project tracker (marginalia) is the source of truth, served from the MacMini at `http://andrews-mac-mini:3100` (the local DuckDB file is a cold mirror — don't query it directly). Each site is a child project of cloudflare-sites #52; query them via the API:

```bash
curl -s 'http://andrews-mac-mini:3100/api/projects?ancestor=52' | jq '.projects[] | {id, name, sourceUrl}'
```

A `deployments` table also records platform/url/target_name per project (`SELECT … FROM deployments WHERE project_id = 52`), but the per-site child projects above are the primary record now.

## Related

- [purescript-hylograph-libs](https://github.com/afcondon/purescript-hylograph-libs) — Published libraries
- [purescript-hylograph-showcases](https://github.com/afcondon/purescript-hylograph-showcases) — Showcase apps
- [hsch-inc](https://github.com/afcondon/hsch-inc) — Source of the three `hylograph-*` sites and `signal-box/`; checked out locally as `polyglot/purescript-polyglot-site`
- [HeresiarchHalogen](https://github.com/afcondon/HeresiarchHalogen) — Source of `andrewcondon/` and `heresiarch/`
- [project-marginalia](https://github.com/afcondon/project-marginalia) — Project tracker; cloudflare-sites is #52 under Websites #228
