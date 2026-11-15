# MemoryMesh Harness

[한국어](README.md)

MemoryMesh is a work-continuity setup for projects whose work moves between sessions, agents, and computers. After ending a session or moving to another agent or operating system, you can pick up where you left off without explaining the situation again.

It records progress, decisions, verification results, and remaining work in files inside the project, and the next session or agent reads and applies them.

## Features

- File-based records: one work log, a current-state summary, and handoff documents only when needed. No server or database is involved.
- The same instructions for every agent: `AGENTS.md` holds the shared instructions, and `CLAUDE.md` and `GEMINI.md` only reference it, so Codex, Claude Code, Gemini CLI, Antigravity, and others use the same instructions and records.
- A light instruction structure: only the core rules for every task are read each time; procedures for review, debugging, tests, and similar work are read only for that work.
- One application request: installation, updates, reset, and removal all start with the same request, and you choose the action once.
- No dependencies: core features need only file reading and writing, with no shell, Git, Python, Node.js, account, or plugin.

## Benefits

- When a session ends, not only work results but also decisions, findings, and agreed next steps from the conversation carry over.
- Other agents continue from the same records without applying the setup again.
- Records remain usable on another operating system, which can also tell which checks must be repeated.
- File changes from a session that ended without recording are checked and added at the next start.
- Questions and review requests do not change project files, and only the requested scope of work is carried out.
- Unnecessary reconfirmation and repeated checks are reduced, and result reports contain only what is needed.
- Existing work logs and user instructions are preserved, with backups made before changes.

## Use cases

- Carrying one task across several sessions over days
- Switching between agents because of usage limits or cost
- Continuing on another operating system work started on one
- Resuming after a session is cut off during a long build or bulk edit
- Opening a new session or continuing after context compression in a long conversation
- Settling a direction through discussion and carrying it out later
- Having another agent review or verify the work and continuing with its findings
- Keeping independent records for several subprojects

## Apply MemoryMesh

1. Download [memorymesh-setup.md](memorymesh-setup.md) and place it in the root folder of the project where you want to use it.
2. Open that folder and ask the agent:

```text
Apply memorymesh-setup.md to this project.
```

Use an agent that can read and write project files, such as Codex, Claude Code, Gemini CLI, or Antigravity. Basic installation requires no separate server or skill installation.

Use the same request when starting for the first time or changing an existing configuration. The agent checks the existing installation, version, and records, shows its findings and the available actions, and asks once what you want to do. Simply choose the desired action from the options it presents.

| Action | What it does |
|---|---|
| Install | Creates the necessary records and instructions. |
| Preserve and apply | Updates MemoryMesh while preserving existing records and user instructions. Recommended for existing installations. |
| Back up and reset | Backs up the existing MemoryMesh configuration, then rebuilds it. |
| Remove | Backs up records, then removes MemoryMesh-specific configuration and instructions. |
| Cancel | Stops without changes. |

When applying, existing user instructions outside MemoryMesh blocks that duplicate or conflict with the new instructions are reported, not edited. Reset and removal preserve project materials, unrelated parts of user instructions, existing external work logs, and shared tools. Items with uncertain ownership are retained and reported.

After replacing the setup file with a new copy, use the same request to apply it again. Downloading or replacing the file alone does not update instructions in an existing project. Choose “Preserve and apply” to apply the new instructions while keeping existing records.

## Files created or updated

After you choose an action, the agent briefly lists the files it will create, update, or remove and the backup location before making changes. During a longer operation, it reports the current stage and reasons for delays, and reassesses the plan or method when progress stalls. At the end, it lists the actual changes and whether the operation completed.

Settings and records are kept in the project's `.mesh/` folder. Existing user content and history are preserved while the necessary parts are updated.

| File or folder | Purpose and when it is created |
|---|---|
| `.mesh/CONFIG.md` | Stores the work log location and record settings. |
| `.mesh/PROTOCOL.md` | Stores the core rules for recording and resuming that apply to every task. It is read at the start of each task, so it is kept short. |
| `.mesh/PROCEDURES.md` | Stores procedures needed only for specific work, such as code review, debugging, tests, and rendered UI checks. It is read only for that work. |
| `.mesh/STATE.md` | Summarizes current progress and remaining work. |
| `.mesh/INSTALL.json` | Records the installed components so later updates or removal can identify them. |
| Existing work log or `.mesh/work.log` | Stores work history. An existing log is reused; the default log is created only when no existing one is available. |
| `AGENTS.md` | The shared instruction entry point read by most agents, such as Codex and Antigravity. If the file exists, its MemoryMesh guidance is added or updated. |
| `CLAUDE.md`, `GEMINI.md` | Connect Claude Code and Gemini CLI to the same shared instructions. They contain only a short reference to `AGENTS.md`, not separate instructions, so the instructions are maintained in `AGENTS.md` alone. They are always prepared, whichever agent applies the setup. If a file exists, its user content is kept and only the reference is added. |
| `AGENTS.override.md` | MemoryMesh guidance is added only if this file already exists. It is never created. |
| `.mesh/handoffs/` | Created only when a detailed handoff document is needed. |
| `.memorymesh-backups/` | The default backup location when existing files change. An explicitly configured backup location takes precedence. |

You do not need to create or edit these files yourself. If optional skill files are added, their paths are included in the notice as well.

## Use

After installation, request work as usual. Whichever agent applied the setup, you can continue in other agents, such as Codex, Claude Code, Gemini CLI, or Antigravity, without applying it again. If an agent does not seem to know about earlier work, run the same application request in that agent. It checks the connection and fills in what is missing, or tells you if it finds that its settings prevent the connection.

- At the start of a new task, the agent reads prior records and applies previous decisions and constraints.
- It saves progress before long or interruptible operations and records new information once before responding with task results. You do not need to repeat instructions to save records.
- Paths in records are relative to the project root, and records and verification results note the agent and operating system, so work can continue on another operating system.
- User decisions, findings, and agreed next steps that files cannot show are recorded briefly when they arise, so they survive the end of a session.
- Result reports contain the requested outcome, the evidence needed, and limits that affect the result, without copying record content into the reply.
- If an ancestor folder also has MemoryMesh, the records nearest to the files being worked on are used, and each installation stays independent.
- An existing work log is reused. Otherwise, records are saved to `.mesh/work.log`. You can check the current status in `.mesh/STATE.md`.

When useful skills such as Ponytail or Graphify are already installed, the agent reads and applies relevant guidance. Other installed skills may also be used when relevant to the task. If a skill or its runtime is unavailable, work continues with MemoryMesh's basic procedures, without recommending or requesting installation. Regression tests and rendered web checks are performed only when the task calls for them, and the results identify anything that could not be verified with actual tools.

Requests for information, opinions, improvement assessments, or answer organization authorize inspection and a response only. Findings are not applied automatically, and versions and publications are not changed. User decisions, findings later work depends on, and agreed next steps from such exchanges are still recorded briefly so the next session can continue. A clear request to change files or carry out work authorizes that task and its necessary record updates. When instructions and questions are mixed, only the instructed actions are carried out.

Immediately before changing files or taking external action, the agent checks that the latest request authorizes that action on that target. A clarification such as “review, do not edit” takes precedence over an earlier interpretation. This is not a requirement to ask again for already-authorized work. The agent must not substitute global skills or external repositories for the requested project. If it discovers an unauthorized change, it stops the affected work and reports it, restoring only its own unauthorized changes when the original state and any intervening edits can be verified.

For requested work, the agent sets completion criteria and a verification method internally and proceeds directly. Verification is sized to the risk of the current change, not to the volume of earlier records, and passing checks with unchanged inputs are not repeated. Once the result and required checks are done, it records once and finishes. Simple tasks need no separate plan file or additional approval step.

## Notes and limitations

- MemoryMesh provides written instructions for agents. Its authorization checks do not technically enforce compliance or guarantee that violations can never occur.
- If a session ends abruptly before recording, decisions or intentions from the conversation since the last record may not carry over. Work results left in files are checked and added to the records when the next task starts.
- For an agent on another computer to continue, `.mesh/` and the work log must reach that computer through sync or a repository.
- Automatic recording and resumption may not work if an agent's settings prevent it from reading project instructions or saving files. If instructions are not read, ask the agent once to read `.mesh/PROTOCOL.md` to continue.
- While a tool is running, the agent may be unable to send progress updates until control returns.

## License

[MIT License](LICENSE)
