# Organization Reusable Workflows

This directory holds NereidAI organization-level reusable workflows. Product
repositories keep only thin callers; the build/publication rules live here once
and implement root treaty §19 (Image Build, Publication and Tagging).

## Version model

There is no `VERSION` file in product repositories. The git tag is the single
source of truth: it keeps the Go-style leading `v` (`v0.0.1`), while every
artifact coordinate uses bare SemVer with that prefix stripped
(`VERSION=0.0.1`). SemVer build metadata is deliberately unsupported because
these coordinates must also be valid Docker/OCI tags. Branch builds that have
no release tag use the fixed rolling-dev version `0.0.0-dev`.

## publish-chart.yml

Publishes one product-owned Helm chart to GHCR OCI without copying its source
into another repository. Product release and rolling-development workflows call
the same implementation so dependency resolution, validation, packaging and
publication cannot drift.

The chart source stays at the caller's `chart-path`. `chart-name` is checked
against `Chart.yaml`, and the resulting package is published under
`oci://ghcr.io/nereidai/charts/<chart-name>`. When dependencies are declared,
the workflow registers their declared public HTTPS repositories (rather than
relying on runner-local Helm configuration) and runs locked
`helm dependency build`, then `helm lint` and `helm template`. Optional newline-separated `values-files` and `set-values`
supply the committed or render-only placeholders required by charts whose
defaults intentionally fail closed. Real site secrets never belong in either
input. The packaged archive is rendered again before publication, including a
check that every rendered GHCR suite-owned image reference—including references
embedded in application configuration—equals the package's `appVersion`.

The `channel` and `version-mode` inputs select the version model:

- `release` with the default `version-mode: product`: the caller must be running
  for a protected `v*` tag. The workflow validates the tag and publishes one
  bare-SemVer chart, with `version` and `appVersion` set to that version.
- `release` with `version-mode: upstream`: the workflow preserves both fields
  from the wrapper's `Chart.yaml`. This is for versioned third-party wrappers
  whose application version is independent of the suite release.
- `development`: the caller must be running for the repository's default branch
  and use `version-mode: product`. The workflow publishes
  `0.0.0-dev-<short-sha>` first and the rolling `0.0.0-dev` second. Each
  package's `appVersion` equals its chart version, so charts that default
  suite-owned image tags to `.Chart.AppVersion` select the matching
  commit-qualified images for immutable development installations.

Product chart sources keep the `0.0.0-dev` placeholder; callers never pass a
release version and no `VERSION` file is introduced. Upstream wrappers instead
keep their own pinned chart/application versions. Authentication uses
`helm registry login` (Docker's credential file is not Helm's registry
configuration). Every push is followed by an authenticated `helm pull` and a
byte-for-byte comparison with the pushed archive. After the first publication, set the
new GHCR package's visibility to Public and verify an anonymous pull before
referencing it from public installation documentation.

### Inputs

| Input | Required | Purpose |
|---|---|---|
| `chart-path` | yes | Repository-relative path to one chart directory |
| `chart-name` | yes | Expected chart name and OCI repository name |
| `channel` | yes | `release` or `development` |
| `version-mode` | no | `product` (default) injects channel versions; `upstream` preserves wrapper metadata and is release-only |
| `values-files` | no | Newline-separated committed placeholder values files for lint/render checks |
| `set-values` | no | Newline-separated render-only `key=value` placeholders; never real secrets |

### Caller examples

```yaml
release-chart:
  uses: NereidAI/.github/.github/workflows/publish-chart.yml@v1
  with:
    chart-path: deploy/helm/nereididentity
    chart-name: nereididentity
    channel: release
    values-files: deploy/helm/nereididentity/values-k3s-example.yaml
```

```yaml
publish-dev-chart:
  uses: NereidAI/.github/.github/workflows/publish-chart.yml@v1
  with:
    chart-path: deploy/helm/nereididentity
    chart-name: nereididentity
    channel: development
    values-files: deploy/helm/nereididentity/values-k3s-example.yaml
```

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
| `vendor` | no | `YIQISOFT` | `org.opencontainers.image.vendor` manifest annotation |
| `licenses` | no | `Apache-2.0` | `org.opencontainers.image.licenses` manifest annotation (SPDX expression) |
| `authors` | no | `NereidAI (https://github.com/NereidAI)` | `org.opencontainers.image.authors` manifest annotation |
| `description` | no | empty | `org.opencontainers.image.description`; empty omits the annotation entirely |
| `annotations` | no | empty | Extra newline-separated OCI annotations (`key=value`, optional `index,manifest:` level prefix) appended after the standard set |

### OCI manifest annotations

Every publication attaches the standard OCI 1.0 annotation set to **both the
multi-architecture index and each per-platform manifest** (via buildx
`--annotation`, not Dockerfile `LABEL` — the latter only lands in the image
config, which the GHCR web UI does not render). The workflow derives
`source`/`url`/`documentation` from `github.repository`, `title` from
`image-name`, `version` from the derived SemVer (release) or fixed
`0.0.0-dev` (dev channel), `revision` from the full commit SHA, and
`created` from an RFC 3339 UTC timestamp generated in the run. Product
repositories normally only pass `description`; the other four inputs exist
for exceptions. Caller-provided Dockerfile `LABEL`s still populate the image
config for traditional inspection tools (`docker inspect`, Syft, Trivy), so
the two layers are intentionally complementary.

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
