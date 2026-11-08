# MemoryMesh Harness

[한국어](README.md)

MemoryMesh helps you continue after pausing work or switching agents without having to explain the entire situation again.

- When you stop partway through a task and return to it later
- When you reach a usage limit and need to hand the work to another agent
- When another agent reviews or verifies the work and you continue by applying its findings

It records progress, decisions, verification results, and remaining work in the project so the next session or agent can read and use them.

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

Reset and removal preserve project materials, unrelated parts of user instructions, existing external work logs, and shared tools. Items with uncertain ownership are retained and reported.

After replacing the setup file with a new copy, use the same request to apply it again.

## Files created or updated

After you choose an action, the agent briefly lists the files it will create, update, or remove and the backup location before making changes. During a longer operation, it reports the current stage and reasons for delays, and reassesses the plan or method when progress stalls. At the end, it lists the actual changes and whether the operation completed.

Settings and records are kept in the project's `.mesh/` folder. Existing user content and history are preserved while the necessary parts are updated.

| File or folder | Purpose and when it is created |
|---|---|
| `.mesh/CONFIG.md` | Stores the work log location and record settings. |
| `.mesh/PROTOCOL.md` | Stores shared instructions for recording and resuming work. |
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
- It saves meaningful progress during work and records new information before responding with task results. You do not need to repeat instructions to save records.
- An existing work log is reused. Otherwise, records are saved to `.mesh/work.log`. You can check the current status in `.mesh/STATE.md`.

Requests for information, opinions, or answer organization do not change files. A clear request to change files or carry out work authorizes that task and its necessary record updates. When instructions and questions are mixed, only the instructed actions are carried out.

## Notes and limitations

- Work resumes from saved records. Information that was not saved before an abrupt exit may not carry over.
- Automatic recording and resumption may not work if an agent's settings prevent it from reading project instructions or saving files.
- While a tool is running, the agent may be unable to send progress updates until control returns.
- Reviewed external skill sources are downloaded only when you request it, and the agent does not ask about it during use. Core features work without downloads.

## License

[MIT License](LICENSE)
