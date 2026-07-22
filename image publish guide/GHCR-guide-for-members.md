# Publishing container images to our GitHub organization

All our images live under `ghcr.io/DigitalWaters-fi/<image-name>`.

**Please publish via GitHub Actions, not from your laptop.** No personal tokens are needed, tags stay consistent, and the package is automatically linked to the repo that built it.

---

## 1. Prepare your repo

Your repo needs a file named exactly `Dockerfile` (no extension) in its root. This is the recipe describing what goes into your environment.

**If you've never written one, start from `Dockerfile.sample` in this folder** — it's a minimal, commented skeleton. Copy it in, rename it to `Dockerfile`, and change the base image and the repo URL.

Also copy `dockerignore.sample` in as `.dockerignore` (with the leading dot). It keeps data files and anything sensitive out of the image.

Your Dockerfile should include this label so the package links back to the repo and inherits its permissions:

```dockerfile
LABEL org.opencontainers.image.source=https://github.com/DigitalWaters-fi/YOUR_REPO
```

(The shared workflow also sets this automatically, but having it in the Dockerfile means local builds get it too.)

## 2. Add the workflow

Create `.github/workflows/publish.yml`:

```yaml
name: Publish

on:
  push:
    branches: [main]
    tags: ["v*"]
  pull_request:

jobs:
  publish:
    uses: DigitalWaters-fi/workflows/.github/workflows/publish-ghcr.yml@main
    permissions:
      contents: read
      packages: write
    with:
      image-name: my-image
      push: ${{ github.event_name != 'pull_request' }}
```

Change `image-name` to whatever your image should be called. **Lowercase only** — GHCR rejects uppercase.

> **Check your default branch name.** The example above assumes your repo's default branch is `main`. If yours is `master` (older repos often are), change `branches: [main]` to `branches: [master]` — otherwise nothing happens when you push and the workflow appears to do nothing at all.
>
> You can see the name at the top-left of the repo's Code tab, or run `git branch --show-current`.
>
> The `@main` at the end of the `uses:` line is different — that refers to the shared `workflows` repo, not yours. Leave it as `@main`.

Optional inputs: `dockerfile` (default `./Dockerfile`), `context` (default `.`), `platforms` (default `linux/amd64`; use `linux/amd64,linux/arm64` if you need multi-arch — it is noticeably slower).

## 3. Push

That's it. On a push to `main` you get:

- `ghcr.io/DigitalWaters-fi/my-image:latest`
- `ghcr.io/DigitalWaters-fi/my-image:main`
- `ghcr.io/DigitalWaters-fi/my-image:sha-abc1234`

On a tag `v1.2.3` you additionally get `:v1.2.3` and `:v1.2`. Pull requests build but do not push, so you find breakage before it ships.

**When you tell someone which image to use, give them a fixed version** — `:v1.2.3` or `:sha-abc1234`, not `:latest`. `:latest` moves every time anyone rebuilds, so an analysis that ran fine last month can silently break. A fixed tag always means exactly the same environment, which is what makes results reproducible.

## 4. Make the image public (important)

**GitHub makes every new image private by default. You almost certainly need to change this to public.**

Repo → **Packages** (right sidebar) → your package → **Package settings** → **Change visibility** → Public.

### Why this matters

Your image is stored on GitHub, but it gets *used* somewhere else — on the CSC cPouta servers that run our JupyterHub notebooks, or on a colleague's laptop. When that machine tries to download the image, GitHub either hands it over (public) or asks for a password (private).

The servers running our notebooks have no password to give. So a private image fails to start, usually with an error like `ImagePullBackOff` or `unauthorized`, and your notebook environment never comes up.

Making the image public is the fix, and it costs you nothing:

- **Public image ≠ public code.** If your repository is private, it stays private. Only the built image becomes downloadable.
- Our images are R and Python environments assembled from open-source packages. There is nothing sensitive in them — provided you followed the rule about not baking secrets into images.

If you have a real reason an image must stay private, say so in the team channel and we'll set up the credentials on the cluster side. Don't work around it yourself.

---

## Pulling an image

```bash
# public image
docker pull ghcr.io/DigitalWaters-fi/my-image:v1.2.3

# private image — classic PAT with read:packages
echo $CR_PAT | docker login ghcr.io -u YOUR_USERNAME --password-stdin
docker pull ghcr.io/DigitalWaters-fi/my-image:v1.2.3
```

## If you really must push from your machine

Only for one-off experiments. Use a **classic** personal access token with `write:packages` and `read:packages` (fine-grained tokens do not fully cover GHCR).

```bash
echo $CR_PAT | docker login ghcr.io -u YOUR_USERNAME --password-stdin
docker build -t ghcr.io/DigitalWaters-fi/my-image:dev-yourname .
docker push ghcr.io/DigitalWaters-fi/my-image:dev-yourname
```

Prefix personal experiments with `dev-` so they are obviously not release artifacts. Never commit a PAT to a repo.

---

## Troubleshooting

| Symptom | Cause / fix |
|---|---|
| Workflow never runs, no entry in the Actions tab | Your default branch is `master` but the workflow says `branches: [main]` (or vice versa). |
| `invalid value workflow reference` | The `uses:` line needs all four parts: `owner/repo/.github/workflows/publish-ghcr.yml@main`. |
| `denied: permission_denied` in Actions | Missing `permissions: packages: write` in the caller job, or org package creation is disabled — ping an org admin. |
| `denied` when pushing locally | Token is fine-grained instead of classic, or missing `write:packages`. |
| `invalid reference format` | Uppercase letters in the org or image name. |
| Package exists but nobody can see it | It's private — change visibility (step 4), or the package isn't linked to a repo (add the `image.source` label and re-push). |
| `ImagePullBackOff` or `unauthorized` when a notebook server starts | The image is still private. Change its visibility to public — see step 4. |
| Push works but package has no repo attached | The `org.opencontainers.image.source` label doesn't match a real repo URL in this org. |
| Multi-arch build times out | Drop back to `linux/amd64` unless ARM is genuinely needed. |

## Conventions

- Image name matches the repo name unless there's a reason otherwise.
- Semver tags (`v1.2.3`) on releases; never overwrite an existing version tag.
- Anything anyone else depends on is referenced by a fixed version tag, never `:latest`.
- **Never put secrets inside an image** — no `.env` files, no API keys, no passwords, no credentials of any kind. Deleting a file in a later build step does *not* remove it; it stays recoverable in the image history, and once the image is public, anyone can extract it. Pass secrets in at runtime instead, and ask if you're unsure how.

Questions → ask in the team channel before inventing your own pipeline.

---

## Appendix — for cluster admins only

Ignore this section unless you administer the Kubernetes cluster. If an image genuinely must stay private, the cluster needs stored credentials to pull it:

```bash
kubectl create secret docker-registry ghcr \
  --docker-server=ghcr.io \
  --docker-username=<github-username> \
  --docker-password=<classic PAT with read:packages> \
  --namespace=<namespace>
```

Then reference it in the pod spec (or JupyterHub's `singleuser.image.pullSecrets`):

```yaml
imagePullSecrets:
  - name: ghcr
```

The secret is namespaced, so it must be created in every namespace that pulls the image, and the PAT needs rotating on a schedule. That maintenance cost is the reason public images are the default recommendation.
