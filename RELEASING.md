# Releasing claude-harness

## Cut a release (GitHub)

```
git tag vX.Y.Z && git push origin vX.Y.Z
gh release create vX.Y.Z --title "vX.Y.Z" --notes "..."
```
This publishes the GitHub Release. CI (`ci`) runs on the tag; the wiki redeploys on any `wiki/**` change.

## Publish to PyPI (one-time setup, then automatic)

PyPI publishing uses **Trusted Publishing (OIDC)** -- no API tokens are stored. It is **gated off by
default** so releases stay green until you opt in. To enable:

1. **Configure the Trusted Publisher on PyPI** (your PyPI account; one-time):
   - PyPI -> your project (or "pending publisher" if the project doesn't exist yet) -> Publishing ->
     Add a GitHub publisher: owner `Wombat164`, repo `claude-harness`, workflow `release.yml`,
     environment `pypi`.
   - Docs: https://docs.pypi.org/trusted-publishers/
2. **Opt in in this repo:** Settings -> Secrets and variables -> Actions -> Variables ->
   add `PYPI_ENABLE` = `true`.
3. **(Recommended) Protect the `pypi` environment:** Settings -> Environments -> `pypi` -> require a
   reviewer, so a publish needs a manual approval.

After that, every published GitHub Release runs `release.yml`, builds the wheel/sdist, and publishes to
PyPI via OIDC. Until `PYPI_ENABLE=true`, the PyPI job is skipped (the release is GitHub-only).

## GitHub Pages (wiki)

Pages is served from the `deploy-wiki.yml` workflow (Settings -> Pages -> Source = GitHub Actions). It
redeploys automatically on pushes that touch `wiki/**`. Manual: `gh workflow run deploy-wiki.yml`.

## Versioning

Bump `version` in `pyproject.toml`, `.claude-plugin/plugin.json`, and `claude_harness/__init__.py`
together (they must match), then tag `vX.Y.Z`.
