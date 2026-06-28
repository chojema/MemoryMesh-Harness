# MemoryMesh Setup

Set up this folder as a MemoryMesh shared workspace for AI coding agents.

Apply the steps below from the project root. Preserve existing project content, create or update the MemoryMesh continuity structure, and install Graphify as a user-level assistant skill for the active AI coding harness. MemoryMesh setup does not build the project graph. The initial graph must be generated later from inside the active AI coding assistant with the Graphify skill command.

## Core rules

1. Preserve existing files and directories.
2. Do not overwrite user-authored content.
3. Create missing items and update only clearly marked MemoryMesh-managed blocks.
4. Keep MemoryMesh control and continuity records inside `.mesh/`.
5. Use Graphify for project-wide knowledge graph and generated Markdown Wiki output.
6. Keep work history and generated project knowledge separate.
7. Reapplying this setup in the same folder must be safe.
8. Report actual conflicts, blockers, and preserved user content.

---

## 1. Inspect the current state

Inspect the project root before changing anything.

Check for:

```text
.mesh/
.graphifyignore
AGENTS.md
CLAUDE.md
GEMINI.md
```

Inside `.mesh/`, check for:

```text
PROTOCOL.md
OVERVIEW.md
work.log
INDEX.md
handoffs/
archive/
cold/
reports/
```

Detect when possible:

- operating system and shell;
- active AI coding harness;
- Python version;
- availability of `uv` and `pipx`;
- existing Graphify installation and version.

Use these checks when available:

```bash
graphify --version
python --version
python3 --version
py -3 --version
uv --version
pipx --version
```

Classify the state as:

- `first install` when no usable MemoryMesh structure exists;
- `re-engagement` when an existing MemoryMesh structure is found.

---

## 2. Create the MemoryMesh structure

Create only missing directories.

```text
.mesh/
├─ PROTOCOL.md
├─ OVERVIEW.md
├─ work.log
├─ INDEX.md
├─ handoffs/
├─ archive/
├─ cold/
└─ reports/
```

POSIX shell:

```bash
mkdir -p .mesh/handoffs .mesh/archive .mesh/cold .mesh/reports
```

PowerShell:

```powershell
New-Item -ItemType Directory -Force -Path `
  .mesh, .mesh/handoffs, .mesh/archive, .mesh/cold, .mesh/reports
```

Graphify output is generated later under `graphify-out/` when the user runs the Graphify assistant skill. Do not create or populate `graphify-out/` during setup.

---

## 3. Create or update `.mesh/PROTOCOL.md`

- If the file is absent, create it with the title and managed block below.
- If the managed block exists, replace only the content inside the block.
- If the file exists without the block, preserve it and append the block.
- Preserve and report direct conflicts.

````markdown
# MemoryMesh Protocol

<!-- MEMORYMESH:PROTOCOL:START -->
MemoryMesh preserves work across sessions, context limits, and agent changes. Graphify supplies the project-wide knowledge graph and generated Markdown Wiki after the user runs the Graphify assistant skill.

## Locations

- MemoryMesh protocol, continuity, logs, handoffs, and indexes: `.mesh/`
- Generated project knowledge graph, report, visualization, and Wiki: `graphify-out/`
- Project source of truth: the actual project files outside `.mesh/` and `graphify-out/`

Do not use `graphify-out/` as a work log. Do not write MemoryMesh handoff records into Graphify output.

## Request classification

Classify the request before reading or changing files.

### Query mode

Use Query mode for information, status, progress, summaries, opinions, evaluations, comparisons, counts, and yes-or-no questions.

- Read only what is needed.
- Answer and stop.
- Do not change files.
- Do not append logs.
- Do not install or run tools.
- Do not begin a next task unless the user explicitly requests it.

### Status and opinion rule

When the user asks about status, progress, opinion, or evaluation, report observed facts or the requested assessment and stop.

### Question and command boundary

An interrogative is a question. A verb inside a question is not an instruction. Only an imperative or explicit approval authorizes a change.

For example, “Is file A deleted?” asks for an existence check. It does not authorize deleting file A.

### Read and change boundary

Necessary reads require no permission.

The following are changes and require an explicit command:

- create, edit, delete, move, rename, or overwrite files;
- execute programs or scripts;
- install packages;
- change configuration;
- generate Graphify output.

Applying `memorymesh-setup.md` explicitly authorizes the setup steps in that file.

### Task mode

Use Task mode when the user explicitly requests creation, modification, deletion, movement, execution, installation, configuration, or generation.

Before changing project files:

1. Read `.mesh/PROTOCOL.md`.
2. Read `.mesh/OVERVIEW.md`.
3. Read the recent tail of `.mesh/work.log`.
4. If resuming or taking over work, inspect relevant files in `.mesh/handoffs/` and `.mesh/INDEX.md`.
5. When `graphify-out/graph.json` exists, use a scoped Graphify query or the generated Wiki before broad raw-file searches.
6. Read source files needed to verify Graphify findings before making consequential changes.

`.mesh` is a hidden folder. If search tools skip it, read the required paths directly.

## Graphify knowledge layer

Graphify is the project-wide generated knowledge layer. It must be invoked from inside the active AI coding assistant as a skill command.

Do not run the terminal CLI form `graphify .` as the default way to build the graph. The terminal CLI may use headless extraction backends and can require API keys.

Use the active assistant’s skill command:

```text
/graphify . --wiki
```

For Codex:

```text
$graphify . --wiki
```

The expected output is:

```text
graphify-out/
├─ graph.html
├─ GRAPH_REPORT.md
├─ graph.json
└─ wiki/
```

Use the outputs as follows:

- `GRAPH_REPORT.md`: project-wide overview, important nodes, communities, and suggested questions;
- `graph.json`: structured graph for queries and paths;
- `graph.html`: browser-based visual exploration;
- `wiki/index.md`: entry point to the generated Markdown Wiki.

When a graph exists:

1. Start with a scoped Graphify query, path, or explain request when the question is specific.
2. Use `GRAPH_REPORT.md` or `wiki/index.md` for project-wide orientation.
3. Read the relevant original project files before modifying code, configuration, or documents.
4. Treat graph relationships and generated Wiki text as derived analysis that may contain omissions or incorrect inference.
5. Do not edit generated Graphify files as a substitute for changing the actual project source.
6. Do not copy work logs or handoff notes into the Graphify Wiki.

## Graph freshness

The graph can become stale after project changes.

Mark the graph as potentially stale when a task materially changes:

- architecture;
- dependencies;
- major modules or APIs;
- database schemas;
- project documentation;
- file relationships represented in the graph.

Record a `GRAPH_STALE` event in `work.log`. Do not automatically rebuild unless the user requested a rebuild or the current command explicitly includes it.

After a successful build or update, record `GRAPH_BUILT` or `GRAPH_UPDATED` and update the Graphify section in `.mesh/OVERVIEW.md`.

## `work.log`

`.mesh/work.log` is append-only. Never edit old entries in place. Correct errors with a `CORRECTION` event.

Event header:

```text
### YYYY-MM-DD HH:MM | <model-id> | <EVENT>
```

Event types:

```text
PROJECT_BOOTSTRAPPED, RE_ENGAGED, PROMPT, WORK_START, WORK_END,
FILES_CREATED, FILES_MODIFIED, FILES_MOVED, FILES_DELETED,
DECISION, NOTE, CHECKPOINT, CONTEXT_PRESSURE, HANDOFF,
HANDOFF_RECEIVED, HANDOFF_CLOSED, HANDOFF_RECOMMENDED,
GRAPH_STALE, GRAPH_BUILT, GRAPH_UPDATED,
BLOCKER, CORRECTION, TOOL_INSTALLED, TOOL_CONFIGURED
```

Append each event with one append operation. Do not read, modify, and rewrite the entire log.

## Checkpoints

Leave a `CHECKPOINT` when any of these conditions applies:

- a file is created, moved, or deleted;
- three or more files are modified;
- the task spans implementation, testing, refactoring, documentation, or other phases;
- many files were inspected;
- the user changed direction;
- meaningful progress would be difficult to reconstruct;
- a risky or large change is about to occur.

Format:

```text
### YYYY-MM-DD HH:MM | <model-id> | CHECKPOINT
Summary: <completed work>
Changed files: <paths>
Current focus: <current step>
Remaining: <remaining work>
Risks: <uncertainties, stale graph, missing tests, or blockers>
```

## Context pressure and handoff

Context pressure is high when two or more of these signals apply:

- the conversation is long;
- many files were read;
- several files were changed;
- direction changed more than once;
- many unresolved assumptions remain;
- the next step requires careful continuity;
- earlier constraints are being re-read repeatedly.

Record:

```text
### YYYY-MM-DD HH:MM | <model-id> | CONTEXT_PRESSURE
Reason: <why the current context is risky>
Current state: <short state summary>
Recommendation: <continue, checkpoint, handoff, or fresh session>
```

When another agent or session should continue, create:

```text
.mesh/handoffs/handoff_<topic>.<from-model-id>.md
```

Include:

- work summary;
- changed files;
- decisions;
- current state;
- remaining work;
- known risks;
- graph freshness;
- exact next action.

Log `HANDOFF` when creating it, `HANDOFF_RECEIVED` when taking it over, and `HANDOFF_CLOSED` when finished.

Before stopping a substantial incomplete task, leave at least one of:

- a recent `CHECKPOINT`;
- a `WORK_END` with clear remaining steps;
- a `HANDOFF` with a handoff file;
- a `CONTEXT_PRESSURE` record.

## Rotation and index

Use:

- `.mesh/work.log` for recent hot history;
- `.mesh/archive/` for older detailed logs;
- `.mesh/cold/` for long-term digests;
- `.mesh/INDEX.md` to locate older records and open handoffs.

Move old records rather than delete them. Do not read all historical records by default.

## Maintaining `OVERVIEW.md`

Keep `.mesh/OVERVIEW.md` brief.

Update it when these change:

- project purpose;
- technology stack;
- core structure;
- settled decisions;
- current work;
- open handoffs;
- important constraints;
- Graphify installation or graph status.

## Sensitive information

Do not write API keys, passwords, tokens, session cookies, private credentials, or unnecessary personal data into `.mesh/` or Graphify memory files.

## Uncertainty

When uncertain, proceed conservatively. Record the assumption, uncertainty, and required verification in `work.log` or the handoff file.
<!-- MEMORYMESH:PROTOCOL:END -->
````

---

## 4. Create the control files

Create each file only when absent. Preserve existing project-specific content.

### `.mesh/OVERVIEW.md`

```markdown
# Project Overview

## Purpose
TBD. Fill in after inspecting the project.

## Tech stack or nature of work
TBD.

## Current state
MemoryMesh initialized. Project details not yet captured.

## Settled decisions
None recorded.

## Current work
None recorded.

## Open handoffs
None.

## Graphify
- CLI: installation pending verification
- Assistant skill: pending verification
- Graph status: not generated
- Wiki status: not generated
- Output: `graphify-out/`

## Important constraints
- Read `.mesh/PROTOCOL.md` before changes.
- Read recent `.mesh/work.log` before Task mode.
- Use scoped Graphify queries before broad raw-file searches when a graph exists.
- Verify important Graphify findings against project source files.
- Leave a checkpoint or clear work-end record for substantial work.
```

### `.mesh/work.log`

```text
# MemoryMesh work.log
#
# Append-only event log.
# Header: ### YYYY-MM-DD HH:MM | <model-id> | <EVENT>
# Correct old entries with CORRECTION; never edit them in place.
# Full rules: .mesh/PROTOCOL.md
# ============================================================
```

### `.mesh/INDEX.md`

```markdown
# MemoryMesh Index

## Topics
| Topic | File | Notes |
|---|---|---|

## Open handoffs
| Handoff | From | To | Status | Notes |
|---|---|---|---|---|

## Recent archives
| Date | File | Topic |
|---|---|---|

## Integrations
| Tool | Version | Scope | Status | Notes |
|---|---|---|---|---|
| Graphify | pending | user | pending | Assistant skill not yet verified |
```

---

## 5. Create or update root instruction files

### `AGENTS.md`

Use this managed block:

```markdown
<!-- MEMORYMESH:START -->
## MemoryMesh
This project uses `.mesh/` for work continuity and Graphify for generated project-wide knowledge.

Before changing project files, read `.mesh/PROTOCOL.md`, `.mesh/OVERVIEW.md`, and the recent tail of `.mesh/work.log`.

Questions, status requests, opinions, and evaluations are read-only. A verb inside a question is not a command.

When `graphify-out/graph.json` exists, use scoped Graphify queries or `graphify-out/wiki/index.md` before broad raw-file searches. Verify consequential findings against the original project files.

Use Graphify from inside the active AI coding assistant. Do not use terminal `graphify .` as the default graph build path because headless extraction can require API keys.

Record meaningful work, checkpoints, graph freshness, and handoffs according to `.mesh/PROTOCOL.md`.
<!-- MEMORYMESH:END -->
```

Apply these rules:

- If `AGENTS.md` does not exist, create it with the managed block.
- If the block exists, replace only the content inside the block.
- If the file exists without the block, preserve it and append the block.
- If an existing instruction directly conflicts, preserve it and report the conflict.

### `CLAUDE.md`

Use:

```markdown
<!-- MEMORYMESH:CLAUDE:START -->
Read `AGENTS.md` and `.mesh/PROTOCOL.md` before changing project files.
<!-- MEMORYMESH:CLAUDE:END -->
```

### `GEMINI.md`

Use:

```markdown
<!-- MEMORYMESH:GEMINI:START -->
Read `AGENTS.md` and `.mesh/PROTOCOL.md` before changing project files.
<!-- MEMORYMESH:GEMINI:END -->
```

For each pointer file:

- create it when absent;
- update only its managed block when present;
- preserve all other content.

Do not create `opencode.md`. OpenCode uses `AGENTS.md`.

---

## 6. Install and configure Graphify

The official Python package is `graphifyy`; the installed command is `graphify`.

Install the base package only. Do not install optional backend extras such as `[openai]`, `[gemini]`, `[ollama]`, `[kimi]`, or `[all]`.

### 6.1 Install the base package as a user-level tool

When `graphify --version` succeeds, keep the installed version.

Otherwise use the first available method.

Preferred:

```bash
uv tool install graphifyy
```

Alternative:

```bash
pipx install graphifyy
```

If a PATH update is required, run the tool’s PATH update command when available, such as:

```bash
uv tool update-shell
```

or:

```bash
pipx ensurepath
```

Do not use `pip install` or install into the project virtual environment.

Verify:

```bash
graphify --version
graphify install --help
```

If Python 3.10 or newer, a supported installer, network access, or permission is unavailable:

1. record a `BLOCKER` event;
2. complete every other MemoryMesh step;
3. report Graphify as the only incomplete component.

### 6.2 Register Graphify for the active assistant harness

Register Graphify as a user-level assistant skill for the active AI coding harness only.

Do not install all platforms. Do not use project-scoped `--project` registration unless the user explicitly requested a project-local skill installation.

Use the installed Graphify CLI help as the final authority because command syntax may change between versions.

Current command patterns include:

```bash
graphify install
graphify install --platform codex
graphify install --platform opencode
graphify install --platform gemini
graphify install --platform agents
graphify antigravity install
```

Use the one that matches the active harness.

Examples:

- Claude Code or a generic slash-command assistant: `graphify install`
- Codex: `graphify install --platform codex`
- OpenCode: `graphify install --platform opencode`
- Gemini CLI: `graphify install --platform gemini`
- Antigravity: `graphify antigravity install`

After registration, verify that the installed instructions describe assistant-skill usage, not headless API extraction as the default path.

### 6.3 Create or update `.graphifyignore`

Preserve existing patterns and create or update only this managed block:

```gitignore
# MEMORYMESH-GRAPHIFY:START
.mesh/
graphify-out/
memorymesh-setup.md
# MEMORYMESH-GRAPHIFY:END
```

This prevents operational logs, generated output, and the installer prompt from becoming graph input.

Do not modify `.gitignore`.

Graphify also respects `.gitignore`. If `.gitignore` excludes important project source folders, report that the excluded files will not appear in the graph.

### 6.4 Record Graphify configuration

Append one event to `.mesh/work.log`:

```text
### YYYY-MM-DD HH:MM | <model-id> | TOOL_CONFIGURED
Tool: Graphify
Package: graphifyy
Version: <verified version>
Scope: user
Active harness: <detected and configured harness>
Changed files: <paths>
Initial graph: not generated
Initial Wiki: not generated
Status: <complete or exact blocker>
```

Update the Graphify row in `.mesh/INDEX.md` with the verified version, scope, and status.

Update the Graphify section in `.mesh/OVERVIEW.md`.

---

## 7. Record initialization

Append exactly one setup event to `.mesh/work.log`, in addition to the Graphify configuration event.

For a first install:

```text
### YYYY-MM-DD HH:MM | <model-id> | PROJECT_BOOTSTRAPPED
Vendor: <vendor or unknown>
Harness: <harness or unknown>
Capabilities: <known capabilities>
Context-window: <known value or unknown>
Summary: MemoryMesh continuity initialized and Graphify assistant skill configured.
Graphify: <complete or exact blocker>
Graph and Wiki: not generated
Preserved: <existing files>
Conflicts: <none or summary>
```

For re-engagement:

```text
### YYYY-MM-DD HH:MM | <model-id> | RE_ENGAGED
Summary: Existing MemoryMesh structure inspected and standard setup verified or updated.
Graphify: <complete or exact blocker>
Graph and Wiki: <existing status>
Preserved: <existing files>
Conflicts: <none or summary>
```

Use the current local date and time.

---

## 8. Verify the setup

Verify that these items exist:

```text
.mesh/PROTOCOL.md
.mesh/OVERVIEW.md
.mesh/work.log
.mesh/INDEX.md
.mesh/handoffs/
.mesh/archive/
.mesh/cold/
.mesh/reports/
AGENTS.md
CLAUDE.md
GEMINI.md
.graphifyignore
```

Also verify:

- MemoryMesh managed blocks remain intact;
- existing user-authored instructions were preserved;
- `graphify --version` succeeds, unless an exact blocker was recorded;
- Graphify was registered for the active assistant harness;
- `graphify-out/` was not created merely by setup.

Do not claim success for an unverified component.

---

## 9. Report completion

Keep the final report concise.

State:

- MemoryMesh setup completion;
- Graphify version and active harness registration status;
- preserved conflicts or actual blockers;
- whether the current AI coding assistant should be restarted;
- the Graphify assistant command to run after restart.

Do not tell the user to run terminal `graphify . --wiki` for the initial graph build.

Use the active assistant command.

For Claude Code, Gemini CLI, OpenCode, Antigravity, or a compatible slash-command assistant:

```text
/graphify . --wiki
```

For Codex:

```text
$graphify . --wiki
```

Explain in one sentence that this assistant-skill command creates the project-wide knowledge graph, report, visualization, and Markdown Wiki under `graphify-out/`.

If an actual blocker remains, report only the blocker and what was completed.
