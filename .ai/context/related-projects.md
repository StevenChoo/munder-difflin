# Related projects

## Purpose and scope

Which repositories this checkout relates to, and what an agent may do with each. Read it before a push, a pull request, or a link to the project on GitHub.

## Core concepts

| Repository                                                                        | Relationship | Notes                                                                                                                                           |
| --------------------------------------------------------------------------------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| [`HarnessMD/munder-difflin`](https://github.com/HarnessMD/munder-difflin)         | Upstream     | The project this fork comes from. Its maintainers review PRs and publish the releases.                                                          |
| [`StevenChoo/munder-difflin`](https://github.com/StevenChoo/munder-difflin)       | This fork    | The `origin` remote. No `upstream` remote is configured in a fresh clone.                                                                       |
| [`chaitanyagiri/munder-difflin`](https://github.com/chaitanyagiri/munder-difflin) | Upstream URL | The URL that `package.json`, the README and CONTRIBUTING use for the upstream project.                                                          |
| AgentJail                                                                         | Sibling      | The developer's sandbox wrappers. Every agent this fork starts must run in it. See [`agentjail-isolation`](../research/agentjail-isolation.md). |

## Rules and constraints

- Before you compare with upstream, add the remote: `git remote add upstream https://github.com/HarnessMD/munder-difflin.git`. Then fetch it.
- A PR to upstream contains no `.ai/`, `AGENTS.md` or `CLAUDE.md` changes. These files exist only in this fork.
- Do not open an issue, a discussion or a PR on upstream without the developer's approval. It is public and permanent.
- Local checkouts of other projects go in `.ai/AGENTS.local.md`, not here.

## Authoritative references

- [`CONTRIBUTING.md`](../../CONTRIBUTING.md): the upstream rules for a PR.
- [`.github/PULL_REQUEST_TEMPLATE.md`](../../.github/PULL_REQUEST_TEMPLATE.md): the PR description that upstream expects.
