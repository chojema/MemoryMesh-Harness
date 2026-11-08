# MemoryMesh Harness

[한국어](README.md)

MemoryMesh is a file-based work continuity tool that records work and uses those records in the next task. You can continue in a new session of the same agent or with a different agent.

## Installation

1. Download [memorymesh-setup.md](memorymesh-setup.md) and place it in the root folder of the project where you want to use it.
2. Open that folder and ask the agent:

```text
Apply memorymesh-setup.md to this project.
```

Use an agent that can read and write project files, such as Codex, Claude Code, or Antigravity. Basic installation requires no separate server or skill installation.

The agent checks existing installations and records, then asks once which installation action to take. If your request already specifies the action, it proceeds directly.

| Action | What it does |
|---|---|
| Install | Creates the necessary records and instructions. |
| Preserve and apply | Updates MemoryMesh while preserving existing records and user instructions. Recommended for existing installations. |
| Back up and reset | Backs up the existing MemoryMesh configuration, then rebuilds it. |
| Remove | Backs up records, then removes MemoryMesh-specific configuration and instructions. |
| Cancel | Stops without changes. |

Reset and removal preserve project materials, unrelated parts of user instructions, existing external work logs, and shared tools. Items with uncertain ownership are retained and reported.

## Use

After installation, request work as usual. To continue with another agent, start work in the same project folder.

- At the start of a new task, the agent reads prior records and applies previous decisions and constraints.
- It saves meaningful progress during work and records new information before responding with task results. You do not need to repeat instructions to save records.
- An existing work log is reused. Otherwise, records are saved to `.mesh/work.log`. You can check the current status in `.mesh/STATE.md`.

Questions, status checks, answer summaries, and requests to organize a discussion into a list do not change files. If you explicitly request a file save or an update to existing records, the agent performs that task.

## Update, reset, or remove

After replacing the setup file with a new copy, apply it again to update the installed instructions. To preserve existing records, ask:

```text
Apply memorymesh-setup.md while preserving existing records and user instructions.
```

Use the setup file to request a reset or removal as well.

```text
Follow memorymesh-setup.md to back up and reset the existing MemoryMesh installation.
```

```text
Follow memorymesh-setup.md to remove MemoryMesh.
```
