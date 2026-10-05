# Skills index

**Purpose.** Reusable agent workflows. A skill is a self-contained directory entered through its `SKILL.md`, describing how to carry out a recurring kind of
task in this repository. Open a skill when the task in hand matches its description, and leave the rest closed.

**Not for.** Durable facts about the project, which belong in [`context/`](../context/_index.md). A skill describes how to work; context describes what is true.

**Conventions.**

- Each skill is one directory containing a `SKILL.md` with `name` and `description` frontmatter. The description states when the skill applies, in terms an
  agent can match against a request.
- Skills add guidance. They never override repository rules or a developer's explicit instruction.
- Keep a skill focused on one recurring task. A skill that covers everything is read by nobody.
- A skill holding a `SOURCE.md` is a copy of someone else's work, kept under its own `LICENSE`. Read it rather than edit it, and take problems upstream.

## Index

<!-- BEGIN agent-generated:index-table -->

| File                                                         | Status  | Summary                                                                                                                                                                                 |
| ------------------------------------------------------------ | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`adding-commands/`](adding-commands/SKILL.md)               | Current | Record how a project command must be invoked, in .ai/commands/.                                                                                                                         |
| [`adding-context/`](adding-context/SKILL.md)                 | Current | Adding, organizing, and curating minimal, high-impact context files.                                                                                                                    |
| [`design/`](design/SKILL.md)                                 | Current | Turn research or a request into a specification the developer approves before anything is built.                                                                                        |
| [`documentation-and-adrs/`](documentation-and-adrs/SKILL.md) | Current | Records decisions and documentation.                                                                                                                                                    |
| [`git-flow/`](git-flow/SKILL.md)                             | Current | Branching, commit cadence, and commit message format.                                                                                                                                   |
| [`handoff/`](handoff/SKILL.md)                               | Current | Write or resume a handoff in .ai/handoffs/.                                                                                                                                             |
| [`humanizer/`](humanizer/SKILL.md)                           | Current | Rewrite AI-sounding text so it reads naturally without changing what it says.                                                                                                           |
| [`i-have-adhd/`](i-have-adhd/SKILL.md)                       | Current | Shape output for a reader with ADHD: lead with the next action, number multi-step work, restate state across turns, suppress tangents, give specific time estimates, make wins visible. |
| [`implement/`](implement/SKILL.md)                           | Current | Build an approved specification one task at a time, stopping at anything that needs a decision.                                                                                         |
| [`ponytail/`](ponytail/SKILL.md)                             | Current | Forces the laziest solution that actually works, simplest, shortest, most minimal.                                                                                                      |
| [`research/`](research/SKILL.md)                             | Current | Survey what exists outside this repository before a decision is taken, and write it up as a dated document with sources.                                                                |
| [`rtk/`](rtk/SKILL.md)                                       | Current | Run shell commands through rtk so their output is compressed before an agent reads it.                                                                                                  |
| [`simple-english/`](simple-english/SKILL.md)                 | Current | Write or rewrite technical text with the rules of ASD-STE100 Simplified Technical English so it is clear, unambiguous, and free of AI slop.                                             |
| [`writing-project-docs/`](writing-project-docs/SKILL.md)     | Current | Writing and maintaining Markdown under `.ai/`.                                                                                                                                          |

<!-- END agent-generated:index-table -->
