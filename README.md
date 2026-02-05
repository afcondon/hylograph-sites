# Hylograph Sites

Static sites for the Hylograph ecosystem, deployed via Cloudflare Pages.

## Sites

| Directory | Domain | Description |
|-----------|--------|-------------|
| `docs/` | hylograph.net | Library documentation (4-quadrant) |
| `blog/` | blog.hylograph.net | Hylographic - essays and discussions |
| `polyglot/` | polyglot.hylograph.net | Demo gallery and showcases |

## Deployment

Each directory is deployed as a separate Cloudflare Pages project:

- **hylograph-docs** → `docs/`
- **hylograph-blog** → `blog/`
- **hylograph-polyglot** → `polyglot/`

## Rebuilding

These are built artifacts from [purescript-polyglot](https://github.com/afcondon/purescript-polyglot).

To rebuild:

```bash
# In purescript-polyglot repo
make website          # Rebuilds polyglot site
make blog             # Rebuilds blog
cd site/hylograph-net && ./build.sh  # Rebuilds docs

# Then copy to this repo
cp -r site/hylograph-net/public/* ../hylograph-sites/docs/
cp -r blog/public/* ../hylograph-sites/blog/
cp -r site/website/public/* ../hylograph-sites/polyglot/
```

## Related

- [purescript-hylograph-libs](https://github.com/afcondon/purescript-hylograph-libs) - Published libraries
- [purescript-hylograph-showcases](https://github.com/afcondon/purescript-hylograph-showcases) - Showcase apps
