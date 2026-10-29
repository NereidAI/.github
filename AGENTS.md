# AGENTS.md — NereidAI `.github` (Organization Profile Repository)

This file holds repo-specific rules only. The shared treaty is
[`../AGENTS.md`](../AGENTS.md) (the NereidAI Root Treaty); read it first. It may
be tightened here, never weakened.

## 1. What This Repository Is

- This directory is an **independent git repository**
  (`https://github.com/NereidAI/.github`, branch `main`), nested inside the
  NereidAI workspace — it is not a product repository.
- It is GitHub's organization-level special repository for the **NereidAI**
  GitHub organization. Its one job here is the **organization profile README**
  that GitHub renders at <https://github.com/NereidAI>.
- This is a **public, marketing- and governance-facing** document, read by
  customers, partners, and upstream communities. Factual accuracy and tone are
  treated as production-level concerns.
- There is no code, build, lint, or test suite in this repo. Never invent any
  (no `npm test`, no linters). Verification is content-level (see §8).

## 2. The Two README Files — Never Confuse Them

| File | Role | Editable? |
|---|---|---|
| `profile/README.md` | **The organization profile README** shown on <https://github.com/NereidAI>. When the user says "nereidai 的 README" / "the org README", this is the file. | Yes — this is the working file. |
| `./README.md` | The landing-page README of the `.github` repository itself (currently just `# .github`). | Only with an explicit request. It is never the org profile. |

Do not confuse the profile README with:

- `../NereidOverview/README.md` — the (currently private) source-code entry
  point and deployment/architecture documentation; a **source**, not the public
  page.
- `../nereidai.github.io/` — the separate public website repository. Website
  content is edited there, never copied into here.
- Any product repo README (`../NereidEdge/README.md`, etc.) — per-product scope.

## 3. Scope and Boundaries

Treaty §6 applies fully: sibling repositories and everything outside this repo
are READ-ONLY — read and quote them as sources, never edit. Scratch files go in
the workspace-root `.tmp/.github/` only (treaty §9); never commit them.

Org-wide health files do not exist here yet (no `.github/workflows/`,
`ISSUE_TEMPLATE/`, `PULL_REQUEST_TEMPLATE.md`, org-wide `FUNDING.yml`, etc.).
Adding any such file affects **every repository in the NereidAI organization**;
create or modify one only on an explicit request that names the file.

## 4. Source of Truth for Every Claim

This page promises things to the public. Follow treaty §7: never speculate or
fabricate.

- Product positioning and architecture: verify against
  `../NereidOverview/README.md` first, then the owning product's own
  README/docs/code for current reality (executable truth beats prose).
- Status words in tables are contractual language. Use only the established
  vocabulary and keep every cell truthful:
  - Deployment table: `Available` / `Planned` / `Commercial contract` /
    `Per-engagement roadmap`.
  - Community matrix: `✓` (included), `—` (not in community).
- Preserve qualifiers exactly: NereidRobot is *Planned — working name*;
  NereidEdge/NereidInference relationships to EdgeX are stated precisely
  ("built on top of, tracking upstream", "being prepared for upstream
  contribution"). Do not upgrade or downgrade such wording without an explicit
  request.
- Upstream-community claims must name the real accepted artifact and link to it
  (e.g. the Open Horizon Web UI lives in
  <https://github.com/open-horizon/oh-web-ui-nereid>). Do not describe work as
  pending once it has been accepted upstream.
- Do not alter committed dates or promises (e.g. the open-sourcing timeline) or
  claims ("production proven", governance biographies, awards, advocacy titles)
  without an explicit request — every one must remain attributable to the user
  or a verifiable source.
- Vendor names follow the established form: English name plus Chinese in
  parentheses on first mention (SOPHGO (算能), MetaX (登临), …). Chinese
  otherwise appears only in established compliance terms (等保). The document
  language is English — add no other Chinese prose.

## 5. Document Structure

`profile/README.md` currently has these sections, in order; keep the structure
stable and extend it rather than inventing parallel sections:

1. `## Introduction 👋` — platform pitch, key differentiators, open-source plan
   callout.
2. `## Start Here` — new-user onboarding: points to **NereidOverview** as the
   source-code entry point, with a goal → destination table (whole suite on
   k3s / air-gapped site / single product / contributor).
3. `## Team & Open-Source Governance` — EdgeX TSC, Open Horizon contribution
   (upstream repo `open-horizon/oh-web-ui-nereid`), silicon expertise,
   advocacy. External links only to canonical pages.
4. `## Deployment & Security Posture` — capability/status table.
5. `## Product Suite` — canonical table, suite services vs domain products.
   **NereidOverview is the first row but is not a product**: it is the
   documentation-only entry point (no runtime code), referenced by *Start
   Here* and listed here, but it NEVER appears in the runtime architecture
   diagram. When a product is added/removed/renamed, update this table **and**
   the diagram (§7) **and** every prose mention in the same change (single
   source of truth, treaty §3.6).
6. `## How It Fits Together` — ASCII architecture diagram plus explanatory
   paragraphs (thin suite layer, NereidMobile, NereidInference).
7. `## Community vs Enterprise` — capability matrix.
8. `## License & Support` — Apache-2.0, no free support, commercial terms.
9. `## Commercial Industry Solutions` — NereidFactory / NereidSentinel /
   NereidMedical, explicitly commercial and separate.
10. `## Our Mission`
11. `## Contact` — GitHub org / Discussions only; do not invent emails, phones,
    or addresses.

## 6. Markdown Rules

- GitHub-flavored Markdown only; no raw HTML, no badges, no images, no external
  CSS/JS. Any new asset must be explicitly requested and stored under
  `profile/`.
- Wrap prose at ~100 columns. Keep each table row on one physical line no
  matter how long, matching existing style.
- In-page anchor links must match GitHub's slug algorithm (lowercase;
  punctuation removed; spaces → `-`; an `&` disappears, which is why
  `License & Support` links as `#license--support` with a double hyphen). When
  renaming a heading, grep and fix every `[...](#...)` reference to it in the
  same change.
- External links use canonical, durable URLs (e.g.
  `https://www.apache.org/licenses/LICENSE-2.0`, official EdgeX pages). When you
  add or change one, actually fetch it and confirm it resolves to the intended
  page.
- Do NOT link to `github.com/NereidAI/<repo>` pages. As of 2026-10 every product
  and overview repository is **private** (verify with
  `gh repo view NereidAI/<name> --json visibility`); only `.github` and
  `nereidai.github.io` are public, so such links 404 for every public visitor.
  Spell repository names as bold plain text (e.g. **NereidOverview**). Links
  may be added only once a repo is public; when repositories open, convert the
  existing plain-text mentions to links repo by repo, verifying each URL first.
- Emoji: the document carries exactly one (`👋` in the Introduction heading).
  Do not add more.
- UTF-8, LF line endings, single trailing newline, no trailing whitespace.
- Match the existing voice: concise, factual, declarative; no superlatives
  beyond the ones already approved.

## 7. Architecture Diagram Rules

The diagram in *How It Fits Together* is a hand-maintained ASCII diagram inside
a fenced code block. Most recent commits exist because it is easy to break:

- Box-drawing and dashing glyphs are single-width; keep every box aligned by
  counting characters. Changing one label means re-padding that box and
  re-aligning its peers and connector endpoints.
- Semantics of the line styles are part of the message — preserve them:
  `┄┄` boundary under the commercial layer, `═══` commercial/open-source
  boundary, `┈┈` WAN/intermittent-link boundary, `╭╌╌╮` dashed boxes =
  external (upstream EdgeX core/device services, physical devices).
- Every arrow/box style used in the diagram must be explained in the legend at
  the bottom of the diagram, and vice versa: no glyph without a legend entry, no
  legend entry without a use.
- Text inside the diagram is plain ASCII only — no math-italic or other styled
  Unicode letters (they were deliberately removed).
- Data flow direction is load-bearing (`models ↓ · events ↑`, Forge → Horizon →
  Edge, frames → NereidInference → EdgeX bus); do not reverse or simplify it
  when redrawing.
- Documentation-only entries (NereidOverview) are not runtime components and
  are never drawn here.
- After editing, re-read the whole block in a monospaced context and confirm
  alignment end to end, not just the edited hunk.

## 8. Verification Before Completion

Per treaty §4, finish on "verified". For this repo that means:

1. Every in-page `#anchor` resolves to an actual heading slug.
2. Every added/changed external URL was fetched and resolves correctly; every
   internal `github.com/NereidAI/...` link points at a repo confirmed public.
3. All pipe tables have consistent column counts and the status vocabulary
   matches §4.
4. The diagram is aligned and its legend matches (§7).
5. Product names/qualifiers agree across the suite table, the diagram, and the
   prose — no orphan mentions of a renamed/removed product.
6. Rendered-diff review of the full file, not just the edited lines.
   There is no automated check; do not claim "renders correctly" without
   checking.

## 9. Commits and Publishing

- Treaty §8 applies: never commit, amend, or push unless explicitly requested.
- Use the treaty identity with DCO sign-off:
  `git -c user.name='Jieke Choo' -c user.email='jiekechoo@yiqisoft.cn' commit -s`
- Conventional subjects with `docs:` for this repo (e.g.
  `docs: add NereidInference to product suite and architecture diagram`).
- **Pushing `main` publishes.** The profile goes live publicly at
  github.com/NereidAI immediately after a push; there is no staging
  environment. Treat push as a release action: re-read the full diff first, and
  when asked to push, verify the live page afterwards.
- Never force-push and never rewrite history on `main`.

## 10. Repo Map

```
.github/                    # this repo: github.com/NereidAI/.github (branch main)
├── AGENTS.md               # this file
├── README.md               # repo landing page only — NOT the org profile
└── profile/
    └── README.md           # the organization profile README (the working file)
```
