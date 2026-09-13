---
type: contributor guide
title: Quickstart
description: Route a documentation change to its authored owner, task-specific skill, and proportionate validation boundary. Start a local Mintlify preview without confusing generated output for source.
tags: [quickstart, documentation, development, validation, mintlify]
sources:
  - id: openwiki-source-9361c44d74c0e18006d0d76f
    resource: repo://.agents/skills/README.md
  - id: openwiki-source-4d9cccca7700db7220ec055e
    resource: repo://.github/workflows/_test.yml
  - id: openwiki-source-164e2da859b5277df81c7d94
    resource: repo://.github/workflows/ci.yml
  - id: openwiki-source-8037e2358a2c4f9b2c722a11
    resource: repo://AGENTS.md
  - id: openwiki-source-012f2c78e3b1446dfc35803f
    resource: repo://Makefile
  - id: openwiki-source-b481a230af378c0c50ed9994
    resource: repo://pipeline/commands/dev.py
  - id: openwiki-source-d0cdf44431684bdedf34705a
    resource: repo://pipeline/core/builder.py
  - id: openwiki-source-05ccef8d4cf1698187f20464
    resource: repo://pyproject.toml
  - id: openwiki-source-23775c3de52f3ab95a13cb8b
    resource: repo://README.md
verified:
  - by: openwiki/0.4.3
    at: 2026-09-13T08:18:47.732Z
generated: { by: "openwiki/0.4.3", at: "2026-09-13T08:18:47.732Z" }
---

# Quickstart

This repository builds the Mintlify site at [docs.langchain.com](https://docs.langchain.com). Edit authored inputs in `src/`, their configuration, or their generator; `build/` is disposable generated output and must not be edited. The separately generated API reference at [reference.langchain.com](https://reference.langchain.com/python/) is outside this repository.

## Start a preview

The checkout requires Python 3.13 or later, Node.js, and `uv`. `make install` synchronizes all Python dependency groups, installs project npm dependencies and the global Mintlify CLI, and links skills for Claude Code. Then start the normal edit-preview loop:

```bash
git clone https://github.com/langchain-ai/docs.git
cd docs
make install
make dev
```

Open <http://localhost:3000>. `make dev` performs an initial build, watches `src/`, and runs `mint dev --port 3000` from `build`. If the initial build fails, it exits rather than serving stale output. Use `--skip-build` only when deliberately reusing an existing generated tree.

For direct CLI entrypoints, watcher behavior, clean-build recovery, and renderer troubleshooting, see [Local Development Workflow](/openwiki/workflows/local-development.md).

## Route the change before editing

Read `AGENTS.md` first: it and the byte-identical `CLAUDE.md` hold rules that apply to every task, including the generated-output boundary and page-navigation requirement. Use a matching procedure from the canonical `.agents/skills/` tree for conditional work. Most supported agents discover that tree directly. Claude Code reads `.claude/skills/`, so run `make skills`—also run by `make install`—to link the canonical skills.

| Change | Source and procedure boundary | Go to |
| --- | --- | --- |
| Add, move, rename, remove, or redirect a page | Select the source domain and emitted route, then update the exact `src/docs.json` navigation and any public redirect. | [Adding and Modifying Documentation Pages](/openwiki/operations/adding-pages.md) and `add-docs-page` |
| Revise prose on an existing open PR | Work on the PR head branch; use the actual PR diff. | `docs-edit` |
| Draft, revise, or review prose | Use house-voice guidance while authoring; use diff-scoped review after prose is finished. | `docs-team-voice` or `docs-review` |
| Locate the owner of a visible route, product, or menu item | Route emission and Mintlify placement have separate owners; do not infer either from a directory name. | [Source Directory Map](/openwiki/architecture/source-map.md) |
| Change navigation, redirects, renderer configuration, OpenAPI, preview, or publication behavior | `src/docs.json` is the Mintlify-facing configuration and navigation contract. | [Mintlify Integration](/openwiki/integrations/mintlify.md) |
| Change a reusable snippet, asset, integration table, generator, or build logic | Change the authored input or generator rather than a derived artifact. | [Source Directory Map](/openwiki/architecture/source-map.md) |
| Move inline examples into runnable files | The sample source and generated snippet have a distinct lifecycle. | `docs-code-samples` and [Testing Overview](/openwiki/testing/test-overview.md) |
| Add or revise an agent procedure | Keep universal rules in `AGENTS.md`; put a focused conditional workflow in one canonical `SKILL.md`. | [Agent Authoring Skills](/openwiki/operations/agent-skills.md) |

`src/docs.json` owns site configuration, navigation, and redirects. A visible label need not match a source path: for example, `src/langsmith/fleet/` is presented as **No-code agents**. Find the navigation location in configuration rather than guessing from the filesystem.

Most shared OSS content produces Python and JavaScript routes. OpenWiki and Deep Agents Code are intentional unversioned exceptions; other domains, including Managed Deep Agents, have their own routing rules. Use the source map before adding a route, language entry, or link.

## Validate the boundary you changed

Run `make build` and inspect the affected generated route after a routing, navigation, asset, preprocessing, or shared-input change. Then choose the smallest check that establishes the relevant contract; passing one layer does not validate another.

| Changed boundary | Focused command | Details |
| --- | --- | --- |
| Pipeline, parser, watcher, skill contract, or repository-wide rule | `make test` | Runs pytest against `tests/unit_tests` with network sockets disabled except Unix sockets; narrow with `TEST_FILE=...`. |
| Generated routes, links, and anchors | `make broken-links-with-anchors` | Builds first, then checks the generated Mintlify tree. |
| Source `@[ref]` references | `make check-cross-refs` | Checks source references, independently of built-site links. |
| Runnable example | `make test-code-samples FILES="..."` | Executes the selected source in its actual toolchain and environment. |
| Sample presentation derived from runnable source | `make code-snippets` | Regenerates derived snippet MDX; do not hand-edit the output. |
| Python tooling or spelling | `make lint` | Runs Ruff format/check, `ty`, and Codespell. |
| Authored prose | `make lint_prose` | Installs and runs the pinned Vale executable. |
| Generated integration material | Run its generator or the targeted metadata check | See the page-change and testing guides for the input owner and command. |

Core CI runs on pull requests, pushes to `main`, and manual dispatch. It includes unit, lint, and built-link checks, plus separate cross-reference, external-integration URL, generated-file, and merge-conflict checks. Reproduce the applicable failed gate locally rather than treating every documentation edit as a reason to run every expensive workflow.

For validation scope, live sample requirements, CI selection, and failure interpretation, see [Testing Overview](/openwiki/testing/test-overview.md). For Mintlify-specific link, export, OpenAPI, preview, and deployment boundaries, see [Mintlify Integration](/openwiki/integrations/mintlify.md).

## Before handoff

- Confirm the edited file is an authored source, configuration owner, or generator—not `build/` or another derived artifact.
- For a page lifecycle change, reconcile its emitted route, precise `src/docs.json` placement, and redirect for a retired public URL.
- Inspect the rendered result and run the focused checks for the changed boundary.
- Record checks run and any deliberate exception in the handoff or pull request.

## Related pages

- [Source Directory Map](/openwiki/architecture/source-map.md)
- [Mintlify Integration](/openwiki/integrations/mintlify.md)
- [Adding and Modifying Documentation Pages](/openwiki/operations/adding-pages.md)
- [Agent Authoring Skills](/openwiki/operations/agent-skills.md)
- [Testing Overview](/openwiki/testing/test-overview.md)
- [Local Development Workflow](/openwiki/workflows/local-development.md)
