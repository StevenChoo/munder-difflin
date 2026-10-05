# Project context

## What this is

Munder Difflin is an open-source Electron desktop app. It runs terminal coding-agent CLIs (`claude`, `codex`, `agy`, `gemini` and eight more) as real PTY
processes, shows each agent as an avatar on a Pixi.js office floor, and coordinates them through a file-based "hive". The GOD agent ("Michael") is the
orchestrator that the user talks to.

This checkout is a personal fork. The upstream project is `HarnessMD/munder-difflin`. See [`related-projects.md`](related-projects.md) for the remotes. Defer to
the upstream contributor rules in [`CONTRIBUTING.md`](../../CONTRIBUTING.md) for any change that can go upstream.

The repository also holds the marketing site and the blog. They are not part of the app:

| Path                 | What it is                                                                                            |
| -------------------- | ----------------------------------------------------------------------------------------------------- |
| `src/`, `test/`      | The app: main process, preload bridge, React renderer. Tests are plain `node:test` `.cjs` files.      |
| `docs/`              | The GitHub Pages site for `munderdiffl.in`, plus the built blog output in `docs/blog/`.               |
| `blog/`              | Eleventy source for the blog. It is its own npm package and builds into `docs/blog/`.                 |
| `landing-remotion/`  | Remotion project that renders the clips on the landing page. Its own npm package.                     |
| `seo/`               | SEO keyword map and blog ideas. `seo/SEO_METADATA.md` is the source of truth for page metadata.       |
| `tools/`, `scripts/` | Build helpers (`copy-main-assets`, `ensure-pty-perms`, `check-release-links`) and one-off generators. |

## Current phase

Pre-1.0 and in active release. The version in `package.json` is the release version, and [`CHANGELOG.md`](../../CHANGELOG.md) holds the history. Most upstream
commits are merged contributor fixes and daily blog and SEO work on `seo-engine/*` branches.

## Where design documents live

The project keeps specs at the repository root, not in a specs folder:

- [`SPEC.md`](../../SPEC.md): the terminal plane and the event plane.
- [`HIVE.md`](../../HIVE.md): the multi-agent design (memory, mailboxes, router, GOD agent).
- [`MEMORY_GRAPH_SPEC.md`](../../MEMORY_GRAPH_SPEC.md): the memory graph.
- [`DESIGN.md`](../../DESIGN.md): the visual system. It is canonical for all UI.
- [`docs/ARCHITECTURE.md`](../../docs/ARCHITECTURE.md): the module map. Read it before a first change in `src/`.

Dated plans and research from upstream are in `docs/research/`.

## Repository rules

These come from [`CONTRIBUTING.md`](../../CONTRIBUTING.md) and the CI workflows. An agent must obey them for every change in `src/`:

- The app ships on macOS, Windows and Linux. Build paths with `path.join` and the Node path helpers, never with a hand-built `"a/b"` string. Test a path that
  contains a space.
- Shared behavior stays provider neutral across all supported agent CLIs. Put logic for one provider behind an explicit check. Do not assume Claude Code.
- New UI uses the design tokens only. `src/renderer/src/design/tokens.ts` and `tokens.css` mirror each other: change both or neither.
- One change per pull request. Do not reformat files that the change does not otherwise touch.
- A new runtime dependency needs a reason in the PR description.
- A behavior change or bug fix gets a test in `test/`.
- Every PR needs before and after evidence under `### Before` and `### After` in the description. The `PR evidence` workflow blocks the merge without it.
- No agent writes to the hive git repository. The harness is the single committer, which prevents `index.lock` corruption.
- Never add steps that check out or run PR code to `.github/workflows/pr-evidence.yml`. It runs as `pull_request_target` with a write token.
- Never commit credentials, signing files (`*.p8`, `*.p12`, `.env.signing`) or a PostHog key. Only official release builds get `POSTHOG_KEY`, so a fork build
  sends no telemetry.
- A blog change commits the source in `blog/` together with the rebuilt `docs/blog/` and `docs/sitemap.xml`. The `Build blog` workflow fails on stale output.

The `.ai/` folder, `AGENTS.md` and `CLAUDE.md` belong to this fork only. Upstream does not have them.

## Licensing

The source code is MIT, in [`LICENSE`](../../LICENSE). The bundled pixel art is LimeZu's _Modern Interiors_ under its Complete Version licence, carved out in
[`LICENSE-ASSETS`](../../LICENSE-ASSETS) and listed in `src/renderer/src/assets/ATTRIBUTION.md`. That licence requires credit to LimeZu: never remove the credit
from the README, the app or the site. New art must be original or compatibly licensed, and must get a row in `ATTRIBUTION.md`.
