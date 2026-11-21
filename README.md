# Durish Labs portfolio

The page content lives in `public/index.html`. It features Character Planner, the AddOn Manager and the Forever API Atlas alongside four other WoW: Forever projects. Each project links to its GitHub repository. The featured screenshots come from each project's own repository: the Character Planner's docs, the AddOn Manager's docs, and the Atlas's `screenshots/symbol-explorer.webp`.

## Preview locally

From this directory, run `python -m http.server 8080 --bind 127.0.0.1 --directory public` and open <http://127.0.0.1:8080/>. You can also open `public/index.html` directly. The page has no build step or runtime dependencies in the browser.

## Publish a private image

Use this directory as the root of a GitHub repository so GitHub can find `.github/workflows/publish-image.yaml`. Pull requests and pushes to `main` only build and smoke-test the image; nothing is published per commit. Only a release publishes, using the built-in `GITHUB_TOKEN` to push a Linux AMD64 image to `ghcr.io/<owner>/<repo>`.

To release, merge the change to `main`, then tag the merge commit with a stable SemVer tag and push it, for example:

```sh
git switch main
git pull --ff-only
git tag v1.0.0
git push origin v1.0.0
```

That run publishes the image as `1.0.0`, `1.0` and `latest`, with the version and commit recorded in its OCI labels. Other tags, such as `v1.0` or `v1.0.0-rc.1`, fail the run without publishing. `latest` follows the most recently published release, including if you manually rerun an older release. The Actions run summary lists the published image references.

New GHCR packages default to private. After the first run, check that the package visibility is **Private** and that the account used by the VPS has read access. If the repository or package already exists, confirm that the workflow repository has write access under the package's Actions access settings.

## Deploy behind Caddy

On the VPS, authenticate as the same OS user that will run Docker Compose. Use a classic GitHub personal access token with `read:packages` from an account that can read the package.

```bash
read -rsp 'GHCR read token: ' ghcr_token
printf '\n'
printf '%s' "$ghcr_token" | docker login ghcr.io --username YOUR_GITHUB_USERNAME --password-stdin
unset ghcr_token
```

Choose the instructions that match where Caddy runs. In either case, the existing Caddy instance handles public HTTPS.

### Caddy in Docker

1. Copy `compose.yaml` to the VPS. In a `.env` file beside it, set `PORTFOLIO_IMAGE=ghcr.io/OWNER/REPO:latest`. If Caddy's existing user-defined Docker network is named something other than `caddy`, also set `CADDY_NETWORK` to that name.
2. Run `docker compose up -d --pull always` from that directory. The web container joins the shared network with the alias `durishlabs-portfolio` and publishes no host port. To update to a new release, repeat the command; `--pull always` fetches the new `latest`. To hold a version or roll back, set `PORTFOLIO_IMAGE` to a release tag such as `ghcr.io/OWNER/REPO:1.2.3` and repeat the command; set it back to `latest` to follow releases again.
3. Add this block to the existing Caddyfile. If it already has a block for the apex domain, incorporate the route into that block.

   ```caddyfile
   durishlabs.com {
       reverse_proxy durishlabs-portfolio:80
   }
   ```

### Caddy on the VPS host

Use this command in place of the Compose setup above. Adjust port 8088 if it is already in use.

```sh
PORTFOLIO_IMAGE=ghcr.io/OWNER/REPO:latest
docker pull "$PORTFOLIO_IMAGE"
docker run -d --restart unless-stopped --name durishlabs-portfolio -p 127.0.0.1:8088:80 "$PORTFOLIO_IMAGE"
```

Use `reverse_proxy 127.0.0.1:8088` in the apex domain block. To update, pull the new image first, then stop and remove the existing `durishlabs-portfolio` container before repeating `docker run`. To hold a version or roll back, use a release tag such as `ghcr.io/OWNER/REPO:1.2.3` in place of `latest`.

### DNS and verification

Point the apex A record at the VPS IPv4 address. Set an AAAA record only if the VPS also serves Caddy over IPv6. Validate and reload your existing Caddy configuration using your usual deployment command, then confirm that `https://durishlabs.com/` serves the portfolio. Caddy needs inbound access on its public HTTP and HTTPS ports for the standard automatic HTTPS setup. Existing subdomain routes can continue using their own blocks.

## License

The site's code is under the [MIT license](LICENSE). The screenshots show World of Warcraft names and
artwork, which belong to Blizzard Entertainment.
