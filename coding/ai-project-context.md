# AI project context

Working list. Edit this as the set changes.

Copy this file to any repo. Put the filled files in `docs/ai/` so they stay out of runbooks. A feature prompt then only names the task. These files already answer what, why, where, and how it should look.

Keep each file short and true. A wrong line is worse than a missing line, because the agent follows it.

## The files

| # | File | What the agent uses it for |
| --- | --- | --- |
| 1 | `docs/ai/PRD.md` | What this product is, who it is for, and what done means. |
| 2 | `docs/ai/ARCHITECTURE.md` | Where code lives, how data moves, which folder is the current one. |
| 3 | `.cursor/rules/*.mdc` | How to write code here. Constraints that apply while editing. |
| 4 | `docs/ai/DESIGN.md` | Type, spacing, color, radius, and which components to reuse. |
| 5 | `docs/ai/TASKS.md` | The current job, in order, with the files it touches. |
| 6 | `docs/ai/MEMORY.md` | Decisions already made, so the agent does not reopen them. |
| 7 | `AGENTS.md` | How to behave in this repo, and which files to read first. |
| 8 | `README.md` | How to install, run, and test. |

Four extras. Stop after these.

| File | What the agent uses it for |
| --- | --- |
| `docs/ai/GLOSSARY.md` | Product words mapped to folders and code names. |
| API pointer | The env var, the client folder, and where the real routes live. A section in Architecture is enough. |
| `.env.example` | Key names only. No secrets. |
| One reference screen | A path in Design, plus a Figma URL when a frame exists. |

Also useful when they already exist: tests next to the feature, and a `.cursorignore` that drops `node_modules`, build output, and generated files.

## How the agent should read them

1. `AGENTS.md` — which repo this is.
2. `README.md` — how to run it.
3. `docs/ai/PRD.md` — what the feature is for.
4. `docs/ai/ARCHITECTURE.md` — where it goes.
5. `docs/ai/DESIGN.md` and `.cursor/rules/` — how it should look and how it should be written.
6. `docs/ai/TASKS.md` — the slice to build now.
7. `docs/ai/MEMORY.md` — decisions already made.
8. `docs/ai/GLOSSARY.md` — when a product word does not match a folder name.

## What each file must contain

### `docs/ai/PRD.md`

- One sentence on the product.
- Who it is for.
- The main flows. For each flow: what the user does, what they see, and the empty, loading, and error cases.
- Invariants. Rules that must stay true: who is allowed to do the thing, what must never be calculated on the client, what must come from the server.
- A short out-of-scope list.

No technical design. That belongs in Architecture.

### `docs/ai/ARCHITECTURE.md`

- What this repo owns, and the sibling repos it must not edit.
- Folder map: screens, navigation, API client, state.
- How the client talks to the API, and the env var for the base URL.
- Current versus legacy. A short table when two systems do the same job: the file to use, and the file to leave alone.
- State. For each kind of data: server cache, client store, or device storage. Name the query or store key a change must refresh.
- Auth. Where the token lives, how a request gets it, and what happens on an expired session. Guest versus signed-in, if both exist.
- Navigation. Screen or route name, the params it takes, and the deep link if one exists. New screens use that list.
- Side effects. What else must move when this data changes: cache, analytics event, push, the sibling app.
- What the agent must not change, and what it must not invent. Include generated files, lockfiles, and native project files when the task is only in app code.

### `.cursor/rules/*.mdc`

Short and enforceable. One concern per file.

Use `alwaysApply: true` only for a rule that belongs on every turn. Scope the rest with `globs` so they load when those files are open.

```markdown
---
description: What this rule enforces
globs: "**/*.{tsx,ts}"
alwaysApply: false
---

The rule, in a few lines.
```

Typical rules: reuse the existing HTTP client, do not add a second one, do not invent endpoints, name the command to run after adding a package, list the paths the agent must not edit.

### `docs/ai/DESIGN.md`

- The token source (CSS variables, a theme file, or a token module).
- Type: which size, family, and weight to use for body, titles, and buttons.
- Spacing and radius: which scale to use. No raw one-off pixel values.
- Color: which file or token set.
- Components that already exist (button, input, card, sheet, list row). Extend those.
- Feedback. The one pattern for loading, empty, error, and success (inline message, toast, or full-screen state). Name the component.
- Copy. Where user-facing strings live, and the tone to follow. New text goes there when the project has a string file.
- One finished screen to copy, by path. Add the Figma URL when a frame exists. Map the frame onto the tokens. Do not copy raw pixel sizes out of the design.

### `docs/ai/TASKS.md`

Only work in progress. Each task has:

- The outcome, in one line.
- The screen or route it touches.
- The existing service function to call, when there is one.
- How to prove it. The command to run, or the tap-path a person checks. Include the empty and error case.

Delete a task when it is done. A stale list makes the agent build an old feature.

### `docs/ai/MEMORY.md`

Facts that survived a past mistake. One line each, dated.

Examples: where auth tokens live, which base URL is which client, which UI must not be shared across apps.

This is not a diary and not a second architecture doc.

### `AGENTS.md`

About ten lines.

- What this repo is.
- The run command.
- Read order: PRD, Architecture, Design, then the open task.

Pointers only. Detail stays in the other files.

### `README.md`

The real commands: install, dev, test, and which env file they read. If this disagrees with Architecture, the agent runs the wrong thing. Fix the file that is wrong.

### `docs/ai/GLOSSARY.md`

Product word, then the folder or code name it maps to. About ten lines. Add a row when a name in conversation does not match the code.

### API pointer

A section inside Architecture, not a new doc.

- Env var for the base URL.
- Folder of the HTTP client. New calls go through that client.
- Where the real routes live, so the agent reads them instead of inventing shapes.
- The type or one real response for the endpoint this task uses. The agent copies those fields. It does not add fields the server never sends.

### `.env.example`

Every key the app reads, with an empty or fake value. No secrets. The agent uses the names to pick the right host.

### Reference screen

One path in Design: "New screens match this file." When a Figma frame exists, put the URL next to that path.

## Feature prompt

After the files exist, a task prompt only needs:

- Which repo.
- Which task in `docs/ai/TASKS.md`.
- Anything this task does differently from the PRD.

## Libraries

No library is required for the agent to understand the project. The files above are the requirement. Cursor already searches and reads the repo.

Optional, and only for a large codebase: a code knowledge graph such as [Graphify](https://github.com/Graphify-Labs/graphify) (`graphifyy` on PyPI). It parses the code once into nodes and edges (imports, calls, inheritance). For a named symbol — a function, component, type, or exported constant — the graph can say whether it exists, which file defines it, which files import it, and which functions call it. That is the "does this exist, and where does it come from" claim. Install is outside the app (`uv tool install graphifyy`), then `graphify install` and `/graphify .` in the agent. It writes `graphify-out/` (`graph.json`, `GRAPH_REPORT.md`). Commit the graph only if the team wants a shared map. Add `graphify-out/cache/` to gitignore.

A search already answers "does this name appear." The graph answers the chain: defined here, imported there, called from these functions. It does not track every local variable inside a function. A `const` that only lives in one file is still read in that file.

Graphify does not replace the files above. It cannot tell the agent what the product is, which screen to copy, or which of two type systems is current. `GRAPH_REPORT.md` is a map of the code, not a PRD.

Skip vector databases, embedding indexes, and agent frameworks (LangChain, LlamaIndex, Chroma) inside the app. Those are for building an AI product. They do not make a coding agent better at this repo.

## Leave out

More markdown that repeats these files. Chat history pasted into the repo. Comments that restate the next line of code. A second design writeup when Design already names the tokens. An npm dependency whose only job is to explain the repo to the agent.
