# Commands index

**Purpose.** How this project's commands must actually be invoked, when the correct invocation is not the obvious one. The **Commands** table below is the
artifact; read it before running a build, a test, a deploy or any project tool.

**Not for.** What the project is or expects, which belongs in [`context/environment.md`](../context/environment.md), and workflows an agent follows when a task
matches, which belong in [`skills/`](../skills/_index.md). This folder answers one question only: what do I type.

**Conventions.**

- One row per command. A row that needs more than a sentence of explanation graduates to its own file, and the row links to it.
- Fill in **Never** and **Why**. Without them an agent reaches for the obvious invocation and rediscovers the failure.
- A command whose arguments vary goes behind a fixed entry point, so it can be approved once instead of on every run.
- A script a human would run anyway belongs in the project's own scripts directory; a script that exists only to give an agent a stable entry point belongs in
  `bin/` here. Record the path either way.

## Index

<!-- BEGIN agent-generated:index-table -->

| File | Status   | Summary                                                                  |
| ---- | -------- | ------------------------------------------------------------------------ |
| —    | Reserved | No command has needed a file of its own yet; the table below holds them. |

<!-- END agent-generated:index-table -->

## Commands

| Command             | Use this                                              | Never                                    | Why                                                                                         |
| ------------------- | ----------------------------------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------- |
| Install             | `npm install`                                         | `npm install --ignore-scripts`           | It skips `electron-rebuild`, and the app fails at launch with a `NODE_MODULE_VERSION` error |
| Typecheck           | `npm run typecheck`                                   | `npx tsc --noEmit`                       | The root `tsconfig.json` has no files of its own, so plain `tsc` checks nothing             |
| Build the blog      | `npm --prefix blog ci && npm --prefix blog run build` | Edit files in `docs/blog/`               | `docs/blog/` is generated; the `Build blog` check fails when it differs from a clean build  |
| Check release links | `npm run check:links`                                 | Skip it after a version change           | A version left behind in `RELEASE.md` or `docs/` makes the download links 404               |
| Release             | Nothing: the developer releases                       | `git push` of a `v*` tag, `npm run dist` | A tag publishes a GitHub Release to users; `dist` needs signing secrets                     |
