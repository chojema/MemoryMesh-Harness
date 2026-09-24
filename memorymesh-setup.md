# MemoryMesh Setup

이 문서는 현재 폴더를 AI 코딩 에이전트가 함께 사용할 수 있는 MemoryMesh 작업 공간으로 설정하기 위한 실행 지침입니다.

프로젝트 최상위 폴더에서 아래 절차를 적용합니다. 기존 프로젝트 내용과 업무 일지를 보존하고, 작업 연속성에 필요한 최소한의 파일만 생성하거나 갱신합니다.

## 기본 원칙

1. 기존 파일과 폴더를 보존합니다.
2. 사용자가 작성한 내용을 덮어쓰지 않습니다.
3. 명확하게 표시된 MemoryMesh 관리 블록만 갱신합니다.
4. 기존 업무 일지가 있으면 그 파일을 기준 기록으로 사용합니다.
5. 같은 작업 내용을 여러 일지에 중복 기록하지 않습니다.
6. 현재 상태는 `.mesh/STATE.md`, 시간순 이력은 설정된 업무 일지에 기록합니다.
7. 실제 소스 파일과 문서를 프로젝트의 최종 기준으로 봅니다.
8. 질문과 상태 확인은 읽기 전용으로 처리합니다.
9. 같은 폴더에 다시 적용해도 기존 기록과 사용자 내용을 보존해야 합니다.
10. 확인한 충돌, 보존한 내용과 완료하지 못한 항목을 정확하게 보고합니다.

---

## 1. 현재 상태 확인

변경하기 전에 프로젝트 최상위 폴더와 기존 지침을 확인합니다.

다음 항목의 존재 여부를 확인합니다.

```text
.mesh/
AGENTS.md
GEMINI.md
```

프로젝트 최상위에 `CLAUDE.md`가 있는지도 확인합니다. 있으면 MemoryMesh 관리 블록(`MEMORYMESH:CLAUDE`)과 MemoryMesh와 충돌하는 지침이 있는지 확인합니다. 없으면 새로 만들지 않습니다.

`.mesh/`가 있으면 다음 파일을 우선 확인합니다.

```text
CONFIG.md
PROTOCOL.md
STATE.md
work.log
handoffs/
```

프로젝트 안에서 기존 업무 일지를 찾습니다. 다음 조건을 만족하는 파일을 강한 후보로 봅니다.

- 사용자가 적용 요청에서 업무 일지라고 명시한 파일
- `.mesh/CONFIG.md`에 이미 지정된 파일
- 제목이나 본문에서 업무 일지, 작업 일지, 진행 기록 또는 work log임을 명확히 밝힌 파일
- 최근 작업, 변경 파일, 결정과 다음 행동을 시간순으로 기록한 파일

`CHANGELOG`, 릴리스 노트와 Git 기록만으로는 업무 일지라고 단정하지 않습니다.

업무 일지는 다음 순서로 선택합니다.

1. `.mesh/CONFIG.md`에 지정된 유효한 경로
2. 사용자가 적용 요청에서 명시한 경로
3. 하나만 확인된 명확한 기존 업무 일지
4. 기존 업무 일지가 없을 때 생성하는 `.mesh/work.log`

후보가 여러 개이고 기준 기록을 판단할 근거가 없으면 임의로 선택하거나 새 일지를 만들지 않습니다. 사용자에게 사용할 경로를 확인한 뒤 계속합니다.

다음 기준으로 설치 상태를 구분합니다.

- `first install`: 사용할 수 있는 MemoryMesh 구조가 없는 경우
- `re-engagement`: 기존 MemoryMesh 구조가 확인된 경우

---

## 2. 최소 구조 만들기

`.mesh/`가 없으면 생성합니다. 기본 설치에서는 하위 폴더를 미리 만들지 않습니다.

```text
.mesh/
├─ CONFIG.md
├─ PROTOCOL.md
└─ STATE.md
```

기존 업무 일지가 없을 때만 다음 파일을 추가합니다.

```text
.mesh/work.log
```

다른 세션이나 에이전트에 상세 작업을 넘길 때만 다음 폴더를 만듭니다.

```text
.mesh/handoffs/
```

POSIX 셸에서는 다음 명령으로 기본 폴더를 만들 수 있습니다.

```bash
mkdir -p .mesh
```

PowerShell에서는 다음 명령을 사용할 수 있습니다.

```powershell
New-Item -ItemType Directory -Force -Path .mesh
```

---

## 3. `.mesh/CONFIG.md` 생성 또는 갱신

- 파일이 없으면 아래 제목과 관리 블록으로 만듭니다.
- 관리 블록이 있으면 블록 안의 내용만 갱신합니다.
- 파일은 있지만 관리 블록이 없으면 기존 내용을 보존하고 블록을 덧붙입니다.
- `work_log`에는 프로젝트 최상위 폴더를 기준으로 한 상대 경로를 기록합니다.
- 기존 업무 일지를 사용하면 `work_log_owner`를 `existing`으로 기록합니다.
- `.mesh/work.log`를 새로 만들면 `work_log_owner`를 `memorymesh`로 기록합니다.

```markdown
# MemoryMesh Configuration

<!-- MEMORYMESH:CONFIG:START -->
work_log: <relative-path>
work_log_owner: <existing-or-memorymesh>
state_file: .mesh/STATE.md
handoff_directory: .mesh/handoffs
<!-- MEMORYMESH:CONFIG:END -->
```

설정된 업무 일지 경로가 실제로 존재하는지 확인합니다.

---

## 4. `.mesh/PROTOCOL.md` 생성 또는 갱신

- 파일이 없으면 아래 제목과 관리 블록으로 만듭니다.
- 관리 블록이 있으면 블록 안의 내용만 갱신합니다.
- 파일은 있지만 관리 블록이 없으면 기존 내용을 보존하고 블록을 덧붙입니다.
- 직접 충돌하는 기존 지침은 삭제하지 않고 완료 보고에 표시합니다.

````markdown
# MemoryMesh Protocol

<!-- MEMORYMESH:PROTOCOL:START -->
MemoryMesh preserves project work across sessions, context limits, and agent changes by using a configured work log and a concise current-state file.

## Sources of truth

- Project source of truth: the actual project files and documents.
- Chronological work record: the file configured as `work_log` in `.mesh/CONFIG.md`.
- Current project state: `.mesh/STATE.md`.
- Detailed transfer notes, when needed: `.mesh/handoffs/`.

Do not use an agent-private memory store as the only record of project work. Do not duplicate the same chronological entry in multiple logs.

## Request classification

Classify the request before changing files.

### Query mode

Use Query mode for information, status, progress, summaries, opinions, evaluations, comparisons, counts, and yes-or-no questions.

- Read only what is needed.
- Answer and stop.
- Do not change files.
- Do not append to the work log.
- Do not begin another task without an explicit request.

An interrogative is a question. A verb inside a question is not an instruction. For example, “Is file A deleted?” authorizes an existence check, not deletion.

### Task mode

Use Task mode only when the user explicitly requests creation, modification, deletion, movement, execution, configuration, or record updates.

Before changing project files:

1. Read `.mesh/PROTOCOL.md`.
2. Read `.mesh/CONFIG.md`.
3. Read `.mesh/STATE.md`.
4. Read the recent part of the configured work log.
5. When resuming transferred work, read the relevant file in `.mesh/handoffs/`.
6. Read the original project files required for the task.

`.mesh` is a hidden folder. Read the required paths directly when a search tool skips hidden content.

## Work log

Treat the configured work log as the only chronological cross-agent work record.

- Preserve its existing format and writing style.
- Append new entries; do not rewrite established history.
- Record meaningful work, changed files, decisions, blockers, and remaining actions.
- Correct an earlier entry with a new correction note instead of silently changing history.
- Do not record routine file reads or insignificant actions.

When MemoryMesh owns `.mesh/work.log`, use this compact entry format:

```text
### YYYY-MM-DD HH:MM | <agent-or-model> | <event>
Summary: <what changed or was decided>
Files: <changed paths or none>
Next: <next action or none>
Risks: <known uncertainty or none>
```

Useful event names are `SETUP`, `WORK_START`, `WORK_END`, `DECISION`, `CHECKPOINT`, `HANDOFF`, `BLOCKER`, and `CORRECTION`. Use another clear name when it describes the event better.

## Current state

Keep `.mesh/STATE.md` brief and current. Update it only when the project goal, current work, recent material changes, settled decisions, next action, open handoffs, or risks change.

Do not copy the full work log into the state file. Replace outdated state text instead of accumulating a second chronological history.

## Handoffs

For a simple continuation, update the work log and `.mesh/STATE.md`; no separate handoff file is needed.

Create `.mesh/handoffs/` and a handoff file only when another session or agent needs details that do not fit in the current-state summary. Name it:

```text
.mesh/handoffs/handoff_<topic>.<from-agent>.md
```

Include the work summary, changed files, decisions, current state, remaining work, known risks, and exact next action. Record the handoff in the configured work log and link it from `.mesh/STATE.md`.

## External changes

MemoryMesh does not watch the project folder. Changes made outside the active agent are not automatically added to the work log or state file. Inspect and record them only when the user requests it.

## Sensitive information

Do not store API keys, passwords, tokens, session cookies, private credentials, or unnecessary personal data in MemoryMesh files.

## Uncertainty

When evidence is incomplete, preserve the uncertainty. Record the assumption and the verification needed instead of presenting it as confirmed.
<!-- MEMORYMESH:PROTOCOL:END -->
````

---

## 5. `.mesh/STATE.md` 생성 또는 갱신

- 파일이 없으면 아래 구조로 만듭니다.
- 파일이 있으면 확인된 현재 상태만 갱신합니다.
- 알 수 있는 내용을 `TBD`로 남기지 않습니다.
- 근거가 없는 내용을 추정하지 않습니다.
- 오래된 상태를 누적하지 않고 현재 내용으로 교체합니다.
- 업무 일지의 시간순 기록을 복사하지 않습니다.

```markdown
# Project State

## Purpose
<project purpose or unknown>

## Current objective
<current objective or none recorded>

## Current status
<concise current status>

## Recent material changes
- <change or none recorded>

## Settled decisions
- <decision or none recorded>

## Next action
<exact next action or none recorded>

## Open handoffs
- <handoff path and status or none>

## Risks and uncertainties
- <risk or none recorded>

## Work log
<path configured in .mesh/CONFIG.md>

## Updated
<YYYY-MM-DD HH:MM and agent or model>
```

프로젝트 목적과 현재 상태는 실제 프로젝트 파일과 선택된 업무 일지에서 확인한 범위만 기록합니다.

---

## 6. 기본 업무 일지 생성

기존 업무 일지가 없을 때만 `.mesh/work.log`를 만듭니다.

```text
# MemoryMesh work log
#
# Append new entries. Correct earlier entries with a later CORRECTION note.
# Full rules: .mesh/PROTOCOL.md
# ============================================================
```

최초 설치 기록을 한 번 추가합니다.

```text
### YYYY-MM-DD HH:MM | <agent-or-model> | SETUP
Summary: MemoryMesh continuity initialized.
Files: .mesh/CONFIG.md, .mesh/PROTOCOL.md, .mesh/STATE.md, .mesh/work.log, and managed instruction files
Next: none
Risks: none or <verified issue>
```

기존 업무 일지를 선택한 경우에는 그 일지의 형식과 문체를 유지합니다. 안전하게 추가할 위치와 형식을 판단할 수 있을 때만 설치 사실을 한 줄 기록합니다. 판단하기 어렵다면 기존 일지를 수정하지 않고 완료 보고에 밝힙니다.

---

## 7. 최상위 지침 파일 생성 또는 갱신

### `AGENTS.md`

다음 관리 블록을 사용합니다.

```markdown
<!-- MEMORYMESH:START -->
## MemoryMesh
This project uses `.mesh/` for cross-session and cross-agent work continuity.

Before changing project files, read `.mesh/PROTOCOL.md`, `.mesh/CONFIG.md`, `.mesh/STATE.md`, and the recent part of the work log configured in `.mesh/CONFIG.md`.

Use the configured work log as the only chronological cross-agent work record. Do not duplicate entries in another log or rely on an agent-private memory store as the only project record.

Questions, status requests, opinions, evaluations, comparisons, counts, and yes-or-no requests are read-only. A verb inside a question is not a command.

Keep `.mesh/STATE.md` concise and current. Create a handoff file only when details needed for transfer do not fit in the state summary.

Verify consequential conclusions against the original project files. Preserve existing user-authored content and report conflicts instead of overwriting it.
<!-- MEMORYMESH:END -->
```

적용 규칙은 다음과 같습니다.

- 파일이 없으면 관리 블록으로 만듭니다.
- 관리 블록이 있으면 블록 안의 내용만 교체합니다.
- 파일은 있지만 관리 블록이 없으면 기존 내용을 보존하고 블록을 덧붙입니다.
- 직접 충돌하는 기존 지침은 보존하고 완료 보고에 표시합니다.

### `CLAUDE.md`

`CLAUDE.md`가 없으면 만들지 않습니다. Claude Code는 `AGENTS.md`를 사용합니다.

프로젝트 최상위에 `CLAUDE.md`가 이미 있으면 다음 관리 블록을 사용합니다.

```markdown
<!-- MEMORYMESH:CLAUDE:START -->
Read `AGENTS.md` and `.mesh/PROTOCOL.md` before changing project files.
<!-- MEMORYMESH:CLAUDE:END -->
```

적용 규칙은 다음과 같습니다.

- 관리 블록이 있으면 블록 안의 내용만 교체합니다.
- 관리 블록이 없으면 기존 내용을 보존하고 블록을 덧붙입니다.
- 다른 업무 일지나 메모리 체계를 지정하는 등 MemoryMesh와 충돌하는 기존 지침은 보존하고 완료 보고에 충돌로 표시합니다.
- 하위 폴더의 `CLAUDE.md`, `CLAUDE.local.md`와 사용자 전역 설정 파일은 변경하지 않습니다.

### `GEMINI.md`

다음 관리 블록을 사용합니다.

```markdown
<!-- MEMORYMESH:GEMINI:START -->
Read `AGENTS.md` and `.mesh/PROTOCOL.md` before changing project files.
<!-- MEMORYMESH:GEMINI:END -->
```

`GEMINI.md`는 없으면 만들고, 있으면 MemoryMesh 관리 블록만 갱신합니다. 다른 내용은 보존합니다.

`opencode.md`는 만들지 않습니다. OpenCode는 `AGENTS.md`를 사용합니다.

---

## 8. 기존 설치 다시 적용

기존 MemoryMesh 구조가 있으면 다음 원칙으로 갱신합니다.

- 기존 사용자 파일과 업무 일지를 보존합니다.
- 현재 관리 블록만 새 내용으로 교체합니다.
- `.mesh/CONFIG.md`가 없으면 실제 업무 일지 경로를 확인해 만듭니다.
- `.mesh/STATE.md`가 없으면 기존 개요와 최근 기록에서 현재 정보만 옮겨 만듭니다.
- 기존 폴더와 파일은 자동으로 삭제하지 않습니다.
- 현재 구조에서 쓰지 않는 이전 설치 항목은 완료 보고에 목록으로 표시합니다.
- 삭제 방법은 배포본 `README.md`의 이전 버전 갱신 안내를 참조하도록 알립니다.

---

## 9. 설치 검증

다음 필수 항목을 확인합니다.

```text
.mesh/CONFIG.md
.mesh/PROTOCOL.md
.mesh/STATE.md
AGENTS.md
GEMINI.md
```

다음 조건부 항목도 확인합니다.

- 기존 업무 일지가 없었다면 `.mesh/work.log`가 생성되었는지 확인합니다.
- 기존 업무 일지를 선택했다면 `.mesh/CONFIG.md`의 경로와 실제 파일이 일치하는지 확인합니다.
- 기존 `CLAUDE.md`가 있었다면 `MEMORYMESH:CLAUDE` 관리 블록이 하나만 있고 기존 내용이 보존되었는지 확인합니다.
- 기존 `CLAUDE.md`가 없었다면 새로 만들지 않았는지 확인합니다.
- 인계 문서를 만든 적이 있다면 `.mesh/handoffs/`와 상태 파일의 링크가 일치하는지 확인합니다.

아울러 다음 사항을 검증합니다.

- MemoryMesh 관리 블록의 시작과 끝 표식이 온전합니다.
- 기존 사용자 작성 내용이 보존되었습니다.
- 기준 업무 일지는 하나만 설정되었습니다.
- 현재 상태와 업무 일지에 같은 시간순 기록이 중복되지 않았습니다.
- 확인하지 않은 항목을 성공으로 보고하지 않습니다.

---

## 10. 완료 보고

완료 보고는 간결하게 작성하고 다음 내용을 포함합니다.

- 최초 설치 또는 재적용 여부
- 설정된 업무 일지 경로와 소유 구분
- 생성하거나 갱신한 파일 전체 목록
- 보존한 기존 파일과 사용자 내용
- `CLAUDE.md` 처리 결과(관리 블록 추가, 갱신 또는 파일이 없어 생략)
- 사용하지 않는 이전 설치 항목
- 충돌, 불확실성 또는 완료하지 못한 항목
- 다음에 필요한 행동이 있으면 정확한 한 가지 행동

여러 파일을 변경했다면 일부 예만 들지 말고 변경한 파일 전체를 제시합니다.
