# Research: how to run Munder Difflin with every agent inside AgentJail

**Purpose.** The developer wants Munder Difflin as the manager that starts, coordinates and follows several coding agents, with the office floor on top. Every
agent must keep AgentJail's protection. This decides how the app starts a jailed agent, and how the app itself runs for a first test without the whole machine.
Status: `Draft`. Researched 2026-10-05, updated as decisions land.

**Where the work stands.** Option 3 (launcher prefix in a fork) was decided in the first survey. A code reading in this repository found that the app already
takes an explicit provider beside a free command, which can make the fork change smaller (section 3). Next: probe T2 by hand on Linux, then answer the new open
question about T6.

## Intake

| #   | Problem                                        | What it really is                                    | Answer shape                                      |
| --- | ---------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------- |
| 1   | "Use Munder Difflin to manage my agents"       | A manager and coordinator for several agents at once | Adopt, adapt or build                             |
| 2   | "The agents run in an AgentJail environment"   | The manager starts agents; each must start jailed    | Where the manager calls the AgentJail launcher    |
| 3   | "I like the gamification side"                 | A requirement on the interface, not on the isolation | A selection criterion: it rules out building anew |
| 4   | "Run it for testing without the whole machine" | The app runs on the host with network and updates    | Which app features to turn off, and where it runs |

## 1. What AgentJail is and what it guarantees

AgentJail is the developer's set of sandbox wrappers for command-line coding agents. Linux uses Firejail profiles and shell wrappers. macOS uses Agent Safehouse
(`sandbox-exec`). It is not a container: the agent uses the host's toolchains and logins, and sees only what the jail allows. It limits what an agent can reach,
not what it does inside that reach.

| Command            | Runs                                               |
| ------------------ | -------------------------------------------------- |
| `claude-safe`      | `claude --dangerously-skip-permissions`            |
| `codex-safe`       | `codex --dangerously-bypass-approvals-and-sandbox` |
| `antigravity-safe` | `agy --dangerously-skip-permissions`               |
| `opencode-safe`    | `opencode`                                         |
| `safe <cmd>`       | any command, with no per-agent isolation           |

These commands are shell functions in `agentjail.sh`, loaded from the shell rc file. They are not files on the PATH. A planned next version replaces them with
one `agentjail` binary (`agentjail claude .`). It does not exist yet.

The rules that a change must never break:

1. The project directory is the boundary. The rest of `$HOME` is hidden, apart from the agent's session directory and a short read-only list.
2. The host never runs anything from the project tree.
3. A project widens its own jail only with the developer's approval at a terminal, pinned to the file's SHA-256.
4. Fail closed: an unknown directive or a parse error stops the launch.
5. Credentials arrive only through a named capability (`github`, `runpod`, `docker-build`), read-only and for one project.
6. One agent never sees another agent's credentials.
7. Config that runs on the host (`settings.json`, `hooks/`, `skills/`, `CLAUDE.md`, `AGENTS.md`) is read-only in the jail.
8. No jail reaches the developer's Docker daemon.
9. No jail reaches AgentJail's own install or config.
10. The environment is an allowlist. The jail starts from `env -i`, and any other variable must be passed by name.
11. Every grant and capability prints at launch.

Accepted risks: the network is open; code the agent writes can run; a planted `.git/hooks` or `.git/config` runs on the host at the next git command;
`~/.claude.json` is writable and its `mcpServers` run on the host; the project has no backup.

A Linux jail shows only the binaries on its `private-bin` list: shell and core utilities, `git`, `node`, `npm`, `uv`, Rust, `gcc`, Java, language servers,
`rtk`, `codebase-memory-mcp` and the agent CLIs. `python3` and `make` are not in the default profile. It applies seccomp, `nonewprivs` and `caps.drop all`, with
no `sudo`, no D-Bus and no SSH agent socket.

## 2. How Munder Difflin starts an agent

Read from this repository at `428223ac`. Nothing was run. `package.json` gives version 0.4.6, but the README lists features shipped through v0.5.3.

- The main process starts each agent with `node-pty` (`PtyManager.spawn`, `src/main/pty.ts`): one file and an argv, no shell. The PATH comes from the user's
  login shell, captured with `$SHELL -ilc` (`src/main/shellEnv.ts`).
- The provider decides what the app adds. A `claude` agent gets `--append-system-prompt` with the hive protocol and `--settings <agent dir>/settings.json` with
  hooks. Codex, Gemini and others get hook shims through per-agent config dirs, or a loopback proxy. A `custom` agent has no bridge, and its mail bounces.
- The hive is files under `<harnessHome>/hive/`, a git repo that only the main process commits. Hooks run a bundled script with Electron's own node and report
  over the Unix socket in `HIVE_SOCK`. Claude telemetry goes to a loopback OTLP port.
- The app turns on Claude Code's own sandbox per agent (`hookSettings`, `src/main/hive.ts`), with write access to the cwd, the agent dir and the hive root.
  `failIfUnavailable` stays false, so the agent runs unsandboxed where bubblewrap cannot start.

## 3. Starting a jailed agent

**Symptom.** AgentJail's commands cannot be the agent command as they are.

**Why.**

- The wrappers are shell functions, and `node-pty` starts only files.
- `env -i` removes `HIVE_SOCK`, the `OTEL_*` values and the per-agent config dirs (`CODEX_HOME` and others), so the hooks lose the socket.
- No jail grants the hive root, the agent dir, the socket, or Electron's node in the app install.
- Claude Code's bubblewrap inside Firejail is not measured. Firejail usually blocks the user namespaces that bubblewrap needs. The app then runs the agent
  without it, which is safe here only because Firejail is the outer layer.

The first survey also listed "a command not named `claude` loses hooks and mail". The code reading in this repository does not support that for agents added
through the app. `inferAgentProvider(command, explicit)` (`src/shared/agentProvider.ts`) returns the explicit provider before it looks at the binary. The
Add-agent form keeps the provider picker and the editable command field apart, and sends both to `spawnPty` (`AddAgentModal.tsx`, `src/main/index.ts`). So
"Claude" with the command `/path/to/claude-jail` keeps provider `claude`, with its `--settings` and hooks. Detection on the binary name applies only where no
provider is stored. This is read from code and not tested.

**Options.**

1. **A `claude` shim first on the PATH** that calls `claude-safe` with the app's arguments. It hides the jail behind a fake `claude`.
2. **Jail the whole app.** No app change, but all agents share one jail, so rule 6 breaks. Acceptable only for a first test.
3. **A launcher prefix setting in a fork**, put in front of every resolved agent command in `pty.ts`, for example `agentjail claude <cwd> --`.
4. **Build a separate manager.** Loses the office floor.

**Decided.** Option 3, in a fork the developer maintains. Each agent keeps its own jail, and the jail shows where the agent starts, not behind a fake `claude`.
The fork needs:

- A launcher prefix setting in `pty.ts` `spawn`.
- `HIVE_SOCK`, the `OTEL_*` values and the per-agent config dirs handed to the jail by name.
- The hive root, the agent dir and the app's node handed to the jail as grants, or found by AgentJail from `HIVE_SOCK` and `HIVE_ROOT`.

Provider detection on the agent command, not on the prefix, was also on this list. Section 3's finding can make it unnecessary for agents that store their
provider. The open question below asks whether the setting is still needed, or whether a launcher file in the command field is enough.

AgentJail needs a launcher that is a file. The planned `agentjail` binary is that launcher. Until it ships, a short executable script that sources
`agentjail.sh` and calls `claude-safe "$@"` stands in. AgentJail also needs a capability (for example `munder-difflin`) that grants the hive and passes its
variables. That is T5.

## 4. Running the app for a test

**Symptom.** The app runs on the host with network access, telemetry, tunnels and an updater.

**Why.** It is an Electron app with a full main process. Read from code, not run:

| Feature           | When it is active                                                                         | How to turn it off                                                                        |
| ----------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| PostHog telemetry | Only builds with `POSTHOG_KEY` injected; `release.yml` adds it                            | A source build has no key and sends nothing. Also `DO_NOT_TRACK=1`, or Settings → Privacy |
| `tunnelmole`      | Slack integration on (`slackEnabled` and a signing secret), or an enabled inbound webhook | Leave Slack and webhooks off. Both are off by default                                     |
| `localtunnel`     | Listed in `package.json`; no import found in `src/`                                       | Nothing to do                                                                             |
| Auto-update       | Packaged apps only (`app.isPackaged`); `autoUpdate` config, default on                    | `npm run dev` never checks. Or set `autoUpdate` off in Settings → General                 |

**Options.**

1. The whole app in one jail (`safe npm run dev`). Needs a display, Electron's Chromium sandbox (likely `--no-sandbox` in Firejail), and `python3` and `make`
   for the native build. All agents share the jail.
2. The app on the host as a source build under a separate Linux user or in a VM, with agents jailed by AgentJail.
3. The app on the host as a source build with Slack and webhooks off, and agents jailed through the launcher.

No decision yet. See the open questions.

## Remaining tasks

| #   | Task                                                                     | Status                                            |
| --- | ------------------------------------------------------------------------ | ------------------------------------------------- |
| T1  | Choose how the app starts a jailed agent (section 3)                     | Decided                                           |
| T2  | Probe: a jailed `claude` with the app's `--settings` reaches `HIVE_SOCK` | Open                                              |
| T3  | Probe: Claude Code's bubblewrap sandbox inside Firejail                  | Open                                              |
| T4  | Read what PostHog, `tunnelmole` and the updater send, and when           | Done by code reading; not observed on the network |
| T5  | Specify the AgentJail hive capability: its grants and passed variables   | Open                                              |
| T6  | Fork: launcher prefix setting                                            | Open                                              |
| T7  | Stand-in launcher script until the `agentjail` binary ships              | Open                                              |
| T8  | Probe: "Claude" provider with a launcher file as command keeps the hooks | Open                                              |

## Open questions

- ~~What is the project's name and repository?~~ Munder Difflin, `https://github.com/HarnessMD/munder-difflin`. The app's metadata still names
  `chaitanyagiri/munder-difflin`.
- ~~Does the manager run on the host, or jailed beside its agents?~~ On the host. Each agent is jailed through the launcher.
- If T8 passes, is the fork's prefix setting still needed, or does a launcher file in the command field cover it?
- Which way does the app run for the first test (section 4)?
- Is macOS in scope now, or Linux first? The developer's needs list says Linux first.

## Traps

- Probe a jail by reading a file, never with `[ -e ]` or `ls -d`. A denied path in Firejail still exists. `wc -c <file>` gives "Permission denied" when the jail
  works.
- A shell opened before AgentJail was installed runs old wrappers with no isolation. Inside a jail, `echo "$AGENTJAIL_ISOLATED"` must print a value. If it is
  empty, run `exec zsh` and start again.
- A checkout's `.git/hooks` is someone else's code. Run git in an upstream clone only from inside a jail.

## Sources

- [HarnessMD/munder-difflin](https://github.com/HarnessMD/munder-difflin): cloned 2026-10-05 at `428223ac`. `README.md`, `HIVE.md`, `src/main/pty.ts`,
  `src/main/hive.ts`, `src/shared/agentProvider.ts`, `src/shared/hire.ts`. Code reading only.
- This repository at `428223ac`, read 2026-10-05: `src/renderer/src/components/AddAgentModal.tsx`, `src/main/index.ts`, `src/main/analytics.ts`,
  `src/main/updater.ts`, `src/main/webhook.ts`, `src/main/slack.ts`, `TELEMETRY.md`, `.github/workflows/release.yml`. Code reading only.
- AgentJail's own repository, notes written 2026-10-05: what AgentJail is, its rules, its Linux jail, and its planned `agentjail` binary.
