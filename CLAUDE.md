# CLAUDE.md

Instructions for AI coding agents working in this repo.

## What this is

The static portfolio site for durishlabs.com, showcasing the WoW: Forever projects.
Content is hand-written HTML/CSS in `public/` (no build step, no JS framework), served by
nginx (`nginx.conf`) in a Docker image published to GHCR and run behind Caddy on a VPS.

## Commands

Preview: `python -m http.server 8080 --bind 127.0.0.1 --directory public`, then open
<http://127.0.0.1:8080/>.

Check the image builds: `docker build -t portfolio-test .`

Publishing (`publish-image.yaml`): pull requests and pushes to `main` only build and
smoke-test the image. Only a stable `vX.Y.Z` tag publishes, as `X.Y.Z`, `X.Y` and `latest`;
never publish per-commit (SHA) images. The VPS deploys `latest` and pins `X.Y.Z` to hold or
roll back. Tagging and deploying are the user's call; the README has the deploy steps.

## Design spec

`DESIGN.md` is the shared look of the Durish Labs sites (this one, the Character Planner and
the Forever API Atlas), derived from `public/styles.css`. Follow it here, and keep the two in
step: a change to a token or component in one goes in the other in the same PR. The other
sites copy the tokens by hand, so when a token changes, open an issue in
`thechainercygnus/character-planner` and `thechainercygnus/ForeverAPIAtlas` to follow.

## Rules

- Screenshots come from the projects' own repos (Character Planner's docs, AddOn Manager's docs, the
  Forever API Atlas's `screenshots/`). Replace
  them from there rather than retaking them here. Keep image sizes small (WebP/JPEG).
- Keep `sitemap.xml`, `robots.txt`, `404.html` and meta/Open Graph tags in step with page
  changes.
- Check pages at phone width and at 200% zoom, with good contrast and alt text. The site is
  dark-only (DESIGN.md), so also check it looks the same with the OS set to light.
- Pin the nginx base image by tag and digest, together. Pin Actions to a full commit SHA
  with the version as a comment; give each job only the permissions it needs.
- CI: `secret-scan`, `codeql` and `dependency-review` must pass.
- Never commit `.env`, tokens or VPS details.
- Commits and pushes need the user's go-ahead. Work on a branch, open a PR.
