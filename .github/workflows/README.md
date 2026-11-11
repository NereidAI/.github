# Organization Reusable Workflows

This directory holds NereidAI organization-level reusable workflows. Product
repositories keep only thin callers; the build/publication rules live here once
and implement root treaty §19 (Image Build, Publication and Tagging).

## Version model

There is no `VERSION` file in product repositories. The git tag is the single
source of truth: it keeps the Go-style leading `v` (`v0.0.1`), while every
artifact coordinate uses bare SemVer with that prefix stripped
(`VERSION=0.0.1`). Branch builds that have no release tag use the fixed
rolling-dev version `0.0.0-dev`.

## release-image.yml

Publishes one suite-owned image to GHCR when a new `v*` git tag is pushed. The
workflow enforces, in one place:

- tag-only triggering (the caller declares `on: push: tags`; there is no
  `workflow_dispatch` path);
- fail-fast SemVer validation of the tag (`vMAJOR.MINOR.PATCH` with an
  optional pre-release suffix) and derivation of bare `VERSION` by stripping
  the leading `v`;
- automatic `VERSION=<bare-semver>` Docker build-arg injection (callers do
  not pass it; extra build-args are appended below it);
- a multi-architecture `linux/amd64,linux/arm64` index via QEMU + buildx,
  with hardware-bound single-arch images as the only documented exception
  (treaty §19.2.1);
- the three-tag scheme only — `VERSION`, `VERSION-<utc-timestamp>` and
  `VERSION-<git-short-sha>` — never `latest`. In the action input the
  traceability tags come first and the bare `VERSION` tag last: GHCR
  displays tags newest-assignment-first, so this makes `VERSION` the
  package's primary displayed tag while all three keep the same digest;
- authentication with the built-in `GITHUB_TOKEN` (no third-party secret);
- no proxy/mirror build arguments; hosted runners build pristine (treaty §17);
- GitHub Actions layer cache (`cache-from`/`cache-to: type=gha`) so a tag
  build reuses layers warmed by the branch CI; a cold cache falls back to
  a full build with no artifact dependency. Each product `ci.yml` warms
  that cache with its own `docker/build-push-action@v6` step
  (`push: false`, same architectures/inputs as the release); the action
  form is required because the `gha` cache backend needs the Actions
  runtime integration that a shell `docker buildx` call does not have.
  Repositories publishing more than one image pass the optional
  `cache-scope` input with one stable, distinct scope per image and use
  the identical `type=gha,scope=<name>` coordinates in the matching CI
  warm steps. Without named scopes every gha export rewrites the shared
  versioned cache index with only its own layer chains, so the second
  image's export makes the first image's chains undiscoverable and its
  tag build goes cold despite a green warm job.

### Inputs

| Input | Required | Default | Purpose |
|---|---|---|---|
| `image-name` | yes | — | GHCR package name under `ghcr.io/nereidai/` |
| `platforms` | no | `linux/amd64,linux/arm64` | Platform list; vendor exceptions pass `linux/amd64` |
| `context` | no | `.` | Build context |
| `file` | no | `Dockerfile` | Dockerfile path |
| `target` | no | empty | Multi-stage build target |
| `build-args` | no | empty | Extra newline-separated build args (never proxy/mirror endpoints); bare `VERSION` is injected automatically |
| `build-contexts` | no | empty | Newline-separated named build contexts as `name=path` |
| `cache-scope` | no | empty | BuildKit gha cache scope; multi-image repos pass one stable distinct scope per image, identical to their CI warm step. Empty renders the bare `type=gha` behavior |

The workflow itself runs with `contents: read` + `packages: write`. It never
creates a GitHub Release: callers that want one add a second job with a
job-level `contents: write` permission, so image-only callers keep the
smallest possible token (GitHub intersects caller/callee permissions).

### Caller example

Every product repository ships a short `.github/workflows/release.yml`:

```yaml
name: Release
on:
  push:
    tags: ['v*']
permissions:
  contents: read
  packages: write
jobs:
  release-image:
    uses: NereidAI/.github/.github/workflows/release-image.yml@v1
    with:
      image-name: nereidlink
      file: deploy/images/nereidlink/Dockerfile
  # Optional: only for repositories that publish a GitHub Release entry.
  github-release:
    needs: release-image
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v7
      - env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          if gh release view "${GITHUB_REF_NAME}" >/dev/null 2>&1; then
            echo "Release ${GITHUB_REF_NAME} already exists"
          else
            gh release create "${GITHUB_REF_NAME}" --generate-notes
          fi
```

## publish-dev-image.yml

The rolling dev channel. A product's `.github/workflows/publish-dev.yml`
calls it on every push to the default branch (never on pull requests, so
forks cannot publish):

```yaml
name: Publish Dev Image
on:
  push:
    branches: [main]
permissions:
  contents: read
  packages: write
jobs:
  publish-dev-image:
    uses: NereidAI/.github/.github/workflows/publish-dev-image.yml@v1
    with:
      image-name: face-service
```

Each run publishes two tags sharing one index digest: the floating
`0.0.0-dev` (moved to the new HEAD, listed last so GHCR shows it primary) and
the immutable `0.0.0-dev-<short-sha>` for traceability and rollback. It takes
the same inputs as `release-image.yml` except there is no tag input; the dev
version is fixed. Dev and release share the same gha cache, and the branch
`ci.yml` warm job uses `VERSION=0.0.0-dev` so its cache keys match.

Callers pin a semver ref (`@v1`); the tag moves forward as the workflow
evolves and no external consumers pin a digest yet. Repositories still
carrying the retired VERSION-file contract must remove that file and the
`VERSION=${{ github.ref_name }}` caller build-arg before their next tag run,
or the duplicated build-arg overrides the workflow's bare-SemVer injection.
After the first publication, set the GHCR package visibility to Public once
in the GitHub UI so anonymous pulls and node-level containerd mirrors can
read it.

### Multiple images from one repository

Declare one caller job per image and give every image its own `cache-scope`,
matching the scopes used by the repository's CI cache-warm job byte for byte:

```yaml
jobs:
  release-backend:
    uses: NereidAI/.github/.github/workflows/release-image.yml@v1
    with:
      image-name: myproduct-backend
      file: deploy/images/backend/Dockerfile
      cache-scope: myproduct-backend
  release-frontend:
    uses: NereidAI/.github/.github/workflows/release-image.yml@v1
    with:
      image-name: myproduct-frontend
      file: deploy/images/frontend/Dockerfile
      cache-scope: myproduct-frontend
```

The CI warm side uses the same two strings on its `cache-from`/`cache-to`
lines (`type=gha,scope=myproduct-backend`, `type=gha,scope=myproduct-frontend`).
Two images sharing the bare default scope overwrite each other's cache index
and silently lose the warm layers.
