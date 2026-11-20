# Durish Labs portfolio

The page content lives in `public/index.html`. It features Character Planner and the AddOn Manager alongside four other WoW: Forever projects. Each project links to its GitHub repository. The featured screenshots come from the Character Planner and AddOn Manager project documentation.

## Preview locally

From this directory, run `python -m http.server 8080 --bind 127.0.0.1 --directory public` and open <http://127.0.0.1:8080/>. You can also open `public/index.html` directly. The page has no build step or runtime dependencies in the browser.

## Publish a private image

Use this directory as the root of a GitHub repository so GitHub can find `.github/workflows/publish-image.yaml`. The workflow runs on pushes to `main`, on pushes of release tags beginning with `v`, and when started manually from the Actions tab. It uses the built-in `GITHUB_TOKEN` to publish a Linux AMD64 image at `ghcr.io/<owner>/<repo>:<full-commit-sha>`.

For a release, create and push a stable SemVer Git tag on the commit you want to ship, for example:

```sh
git tag v1.0.0
git push origin v1.0.0
```

That run publishes the same image under the commit SHA, `1.0.0`, and `latest`. Branch builds publish only the commit SHA. `latest` follows the most recently published stable release, including if you manually rerun an older release. The Actions run summary lists the published image references. Set `PORTFOLIO_IMAGE` to a specific version for a predictable deploy or rollback.

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

1. Copy `compose.yaml` to the VPS. In a `.env` file beside it, set `PORTFOLIO_IMAGE` to the image reference from the Actions run summary. If Caddy's existing user-defined Docker network is named something other than `caddy`, also set `CADDY_NETWORK` to that name.
2. Run `docker compose up -d --pull always` from that directory. The web container joins the shared network with the alias `durishlabs-portfolio` and publishes no host port. For updates or rollbacks, set `PORTFOLIO_IMAGE` to the desired release's image reference and repeat the command.
3. Add this block to the existing Caddyfile. If it already has a block for the apex domain, incorporate the route into that block.

   ```caddyfile
   durishlabs.com {
       reverse_proxy durishlabs-portfolio:80
   }
   ```

### Caddy on the VPS host

Use this command in place of the Compose setup above. Adjust port 8088 if it is already in use.

```sh
PORTFOLIO_IMAGE=ghcr.io/OWNER/REPO:FULL_COMMIT_SHA
docker pull "$PORTFOLIO_IMAGE"
docker run -d --restart unless-stopped --name durishlabs-portfolio -p 127.0.0.1:8088:80 "$PORTFOLIO_IMAGE"
```

Use `reverse_proxy 127.0.0.1:8088` in the apex domain block. To update, pull the new image first, then stop and remove the existing `durishlabs-portfolio` container before repeating `docker run` with the new image reference.

### DNS and verification

Point the apex A record at the VPS IPv4 address. Set an AAAA record only if the VPS also serves Caddy over IPv6. Validate and reload your existing Caddy configuration using your usual deployment command, then confirm that `https://durishlabs.com/` serves the portfolio. Caddy needs inbound access on its public HTTP and HTTPS ports for the standard automatic HTTPS setup. Existing subdomain routes can continue using their own blocks.

## License

The site's code is under the [MIT license](LICENSE). The screenshots show World of Warcraft names and
artwork, which belong to Blizzard Entertainment.
