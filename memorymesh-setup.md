# MemoryMesh Setup

Set up this folder as a MemoryMesh shared workspace for AI coding agents.

Apply the steps below from the project root. Preserve existing project content, create or update the MemoryMesh continuity structure, and install Graphify as a user-level assistant skill for the active AI coding harness. MemoryMesh setup does not build the project graph. The initial graph must be generated later from inside the active AI coding assistant with the Graphify skill command.

## Core rules

1. Preserve existing files and directories.
2. Do not overwrite user-authored content.
3. Create missing items and update only clearly marked MemoryMesh-managed blocks.
4. Keep MemoryMesh control and continuity records inside `.mesh/`.
5. Treat `.mesh/` as the only cross-agent work record location; do not use any agent private or internal store for project work continuity.
6. Use Graphify for project-wide knowledge graph and generated Markdown Wiki output.
7. Keep work history and generated project knowledge separate.
8. Reapplying this setup in the same folder must be safe.
9. Report actual conflicts, blockers, and preserved user content.

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
scripts/
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
├─ reports/
└─ scripts/
```

POSIX shell:

```bash
mkdir -p .mesh/handoffs .mesh/archive .mesh/cold .mesh/reports .mesh/scripts
```

PowerShell:

```powershell
New-Item -ItemType Directory -Force -Path `
  .mesh, .mesh/handoffs, .mesh/archive, .mesh/cold, .mesh/reports, .mesh/scripts
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

Work status, checkpoints, and handoffs must be recorded in `.mesh/` regardless of agent type. Do not use any agent private or internal store for project work continuity, including Claude Code memory, Codex session state, or any equivalent assistant-specific storage. `.mesh/` is the only cross-agent work record location.

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

## Graphify assistant registration

Graphify registration is project-scoped and all-agent by default. Use one project registration script from the project root so agents do not need separate manual setup runs.

The setup-created command is:

```bash
.mesh/scripts/install-graphify-agents.sh
```

This script runs the native Graphify all-agent project registration command first:

```bash
graphify install --project
```

Use this as the primary path because it is maintained by Graphify and can include newly supported agents without MemoryMesh hard-coding every platform. If the installed Graphify version does not support `--project`, the script falls back to documented per-platform registrations.

Some platform registrations only create project or user instruction files for that agent. The agent application or CLI does not always need to be installed at setup time. If one platform registration fails, keep successful registrations, record the failed platform as an exact blocker, and explain that the user can rerun the script after preparing that platform.

Keep `AGENTS.md` as the common entrypoint for all agents. Dedicated hooks or skills may improve integration, but agents without a dedicated Graphify hook must still receive the Graphify and MemoryMesh instructions through `AGENTS.md`.

Fallback per-platform registrations are:

```bash
graphify install --platform claude       # Claude Code
graphify install --platform codex        # Codex
graphify install --platform opencode     # OpenCode
graphify install --platform agents       # Agents-compatible assistant
graphify install --platform antigravity  # Antigravity
```

When a new agent is supported but not covered by `graphify install --project`, add exactly one labeled fallback registration command to `.mesh/scripts/install-graphify-agents.sh` and document it in this section. Do not scatter agent-specific registration instructions across unrelated project files.

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
- Registration script: `.mesh/scripts/install-graphify-agents.sh`
- All-agent registration: pending verification
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
| Graphify | pending | project | pending | All-agent registration script not yet verified |
```

---

## 5. Create or update root instruction files

### `AGENTS.md`

Use this managed block:

```markdown
<!-- MEMORYMESH:START -->
## MemoryMesh
This project uses `.mesh/` for work continuity and Graphify for generated project-wide knowledge.

`AGENTS.md` is the common entrypoint for all agents. Agents without a dedicated Graphify hook or skill still use these instructions.

`.mesh/` is the only cross-agent work record location. Do not use Claude Code memory, Codex session state, or any other agent private/internal store for project work continuity.

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

Keep this file for Antigravity and other Google/Gemini-family tools that read Gemini-style instruction files. Do not treat this as a Gemini CLI registration step.

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

### 6.2 Create and run the project registration script

Register Graphify at the project level for all currently known AI coding agents in one run. Do not require the user or the current agent to run separate commands for Claude Code, Codex, OpenCode, Antigravity, or other known platforms.

Create or update `.mesh/scripts/install-graphify-agents.sh` with this managed script:

```bash
#!/usr/bin/env bash
set -u

if ! command -v graphify >/dev/null 2>&1; then
  echo "BLOCKER: graphify command not found" >&2
  exit 1
fi

failed=0
succeeded=""
failed_labels=""

run_registration() {
  label="$1"
  shift
  echo "==> ${label}: $*"
  if "$@"; then
    echo "OK: ${label}"
    succeeded="${succeeded}${label}; "
  else
    status=$?
    echo "WARN: ${label} registration failed with exit code ${status}" >&2
    failed=1
    failed_labels="${failed_labels}${label}(${status}); "
  fi
}

if graphify install --help 2>&1 | grep -q -- '--project'; then
  run_registration "Graphify all-agent project registration" graphify install --project
else
  echo "WARN: graphify install --project is unavailable; using fallback per-platform registrations" >&2
  run_registration "Claude Code" graphify install --platform claude
  run_registration "Codex" graphify install --platform codex
  run_registration "OpenCode" graphify install --platform opencode
  run_registration "Agents-compatible assistant" graphify install --platform agents
  run_registration "Antigravity" graphify install --platform antigravity
fi

echo "SUMMARY: registered=${succeeded:-none}"
if [ "${failed}" -ne 0 ]; then
  echo "SUMMARY: failed=${failed_labels}" >&2
  echo "Some platform registrations failed. Successful registrations remain in place; rerun this script after resolving the listed platform blockers." >&2
fi

exit "${failed}"
```

Make it executable when the platform supports POSIX permissions:

```bash
chmod +x .mesh/scripts/install-graphify-agents.sh
```

Run exactly one project registration command from the project root:

```bash
.mesh/scripts/install-graphify-agents.sh
```

The script uses `graphify install --project` as the primary path. That command is the universal Graphify project registration point and should work no matter which supported agent applied the MemoryMesh setup first. A project initialized from Codex can later be opened from Claude Code, Antigravity, OpenCode, or another supported agent; the reverse direction should work the same way as long as the all-agent registration succeeded.

Some platform registrations create project or user instruction files and do not require the corresponding agent app or CLI to be installed at setup time. If `graphify install --project` is unavailable, the script falls back to per-platform registrations. If a fallback platform registration fails, the script keeps successful registrations, prints a summary, exits nonzero, and the setup must record only the failed platform as a blocker.

Fallback per-platform registration methods are documented in `.mesh/PROTOCOL.md` and must match the script:

- Claude Code: `graphify install --platform claude`
- Codex: `graphify install --platform codex`
- OpenCode: `graphify install --platform opencode`
- Agents-compatible assistant: `graphify install --platform agents`
- Antigravity: `graphify install --platform antigravity`

For a new agent, first rely on an updated Graphify package and `graphify install --project`. Only add a new fallback line to `.mesh/scripts/install-graphify-agents.sh` and `.mesh/PROTOCOL.md` when the native project installer does not cover that agent. Keep `AGENTS.md` as the common entrypoint for all agents so tools without a dedicated hook or skill still receive the Graphify usage instructions.

After registration, verify that installed instructions describe assistant-skill usage, not headless API extraction as the default graph build path. Report each registration that failed and each registration that succeeded. Do not claim all-agent setup success unless the script completed successfully. When failures remain, state that MemoryMesh setup is complete except for the listed Graphify platform registrations.

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
Scope: project
Registration script: .mesh/scripts/install-graphify-agents.sh
Registered agents: all supported Graphify project registrations, or exact blockers
Changed files: <paths>
Initial graph: not generated
Initial Wiki: not generated
Status: <complete or exact blocker>
```

Update the Graphify row in `.mesh/INDEX.md` with the verified version, project scope, registration script, and all-agent registration status.

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
Summary: MemoryMesh continuity initialized and Graphify project registration script configured.
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
.mesh/scripts/
.mesh/scripts/install-graphify-agents.sh
AGENTS.md
CLAUDE.md
GEMINI.md
.graphifyignore
```

Also verify:

- MemoryMesh managed blocks remain intact;
- existing user-authored instructions were preserved;
- `graphify --version` succeeds, unless an exact blocker was recorded;
- `.mesh/scripts/install-graphify-agents.sh` exists and is executable where POSIX permissions apply;
- Graphify was registered for all currently known assistant harnesses, or exact blockers were recorded;
- `AGENTS.md` remains the common entrypoint for agents without dedicated Graphify hooks or skills;
- `graphify-out/` was not created merely by setup.

Do not claim success for an unverified component.

---

## 9. Report completion

Keep the final report concise.

State:

- MemoryMesh setup completion;
- Graphify version and all-agent project registration status;
- preserved conflicts or actual blockers;
- whether the current AI coding assistant should be restarted;
- the single project registration command and the Graphify assistant command to run after restart.

Do not tell the user to run terminal `graphify . --wiki` for the initial graph build.

Use the active assistant command.

For Claude Code, OpenCode, Antigravity, or a compatible slash-command assistant:

```text
/graphify . --wiki
```

For Codex:

```text
$graphify . --wiki
```

Explain in one sentence that this assistant-skill command creates the project-wide knowledge graph, report, visualization, and Markdown Wiki under `graphify-out/`.

If an actual blocker remains, report only the blocker and what was completed.
