# MemoryMesh Harness

MemoryMesh는 작업 중 유용한 기록을 남기고, 다음 작업을 시작할 때 그 기록을 읽어 반영하는 파일 기반 작업 연속성 체계입니다. 같은 에이전트의 다음 세션과 다른 에이전트로의 순차적 인계를 함께 지원합니다.

셋업 개정 번호는 `2026-11-08.1`, 운영 규칙 개정 번호는 `2`입니다. 작업 기록은 기존 프로젝트의 언어와 형식을 유지합니다.

## 적용 방법

프로젝트 최상위 폴더에 `memorymesh-setup.md`를 넣고 사용할 에이전트에 요청합니다.

```text
memorymesh-setup.md 파일의 설정 내용을 이 프로젝트에 적용하라.
```

같은 파일과 같은 지시로 Codex, Claude Code, Antigravity 등의 초기 적용을 시작하도록 설계했습니다. 에이전트는 파일을 직접 읽고 저장할 수 있어야 합니다. 기본 설치에는 전용 플러그인, 사전 설치된 스킬, 셸, Git, Python, Node.js가 필요하지 않습니다. 셸이 있으면 환경에 맞는 명령으로 같은 절차를 수행합니다.

실행 환경과 기존 설치를 확인한 뒤 변경 계획을 보여주고 설치 방식을 한 번 확인합니다. 방식을 이미 명시했다면 다시 묻지 않습니다.

```text
memorymesh-setup.md를 적용하되, 기존 기록과 사용자 지침을 유지하며 갱신하라.
```

Markdown 파일을 복사하는 것만으로 설치가 실행되지는 않습니다. 에이전트가 셋업 지침을 읽고 적용합니다.

## 설치, 갱신, 초기화, 제거

상태는 신규 설치, 같은 버전 재적용, 새 버전 갱신, 이전 버전 셋업의 적용 가능성, 일부 누락 또는 버전 확인 불가로 구분합니다. 버전 정보가 없는 이전 설치를 신규로 간주하지 않습니다. 날짜와 숫자 순번으로 버전을 비교하며, 다른 버전이라는 이유만으로 업그레이드로 판단하지 않습니다. 이전 버전 적용은 설치 방식 선택 단계에서 명시하고, 사용자가 그 적용을 분명히 선택한 경우에만 진행합니다. 그렇지 않으면 설치된 새 버전을 유지합니다.

| 선택 | 처리 내용 |
|---|---|
| 신규 설치 | 최소 기록과 해당 환경의 지침 진입점을 구성합니다. |
| 기존 내용 유지하며 반영 | 기존 이력과 사용자 내용을 보존하고 관리 영역만 갱신합니다. 기존 설치의 기본 권장 방식입니다. |
| 백업 후 초기화 설치 | 기존 MemoryMesh 구성과 소유 기록을 백업하고 확인된 프로젝트 상태로 다시 구성합니다. |
| MemoryMesh 제거 | 최신 기록을 백업하고 소유권이 확인된 전용 파일과 관리 블록을 제거합니다. |
| 취소 | 변경 없이 종료합니다. |

취소는 즉시 종료하며 백업이나 기록도 만들지 않습니다. 제거는 백업과 제거, 제거 결과 확인만 수행하고 설치 파일을 새로 만들지 않습니다. 초기화는 백업과 보관을 마친 뒤 재구성합니다. 신규 설치와 기존 내용 유지 방식은 필요한 구성 생성 또는 갱신 후 설치 결과를 확인합니다.

초기화와 제거는 MemoryMesh 관리 영역을 대상으로 합니다. 프로젝트 자료, 사용자 지침의 다른 부분, 외부의 기존 업무 일지, 공용 스킬과 실행 환경은 보존합니다. 소유권이 불분명한 항목은 남겨 두고 정확히 보고합니다.

변경할 기존 파일은 `.mesh` 밖의 `.memorymesh-backups/<시각>/` 등에 백업합니다. 같은 버전을 재적용해 바뀔 내용이 없으면 검증만 하고, 설치 기록이나 백업을 반복하지 않습니다. 최신 셋업 파일을 복사한 뒤 다시 적용해야 기존 운영 규칙과 관리 블록도 갱신됩니다.

## 기본 구성

| 위치 | 역할 |
|---|---|
| `.mesh/CONFIG.md` | 버전, 단일 업무 일지, 기록 언어와 시간대, 조건부 스킬 출처 |
| `.mesh/PROTOCOL.md` | 작업 시작, 기록, 재개, 스킬 활용의 공통 규칙 |
| `.mesh/STATE.md` | 현재 목표와 결과, 결정, 검증 범위, 다음 행동 |
| `.mesh/INSTALL.json` | 생성 파일과 관리 블록의 소유권 목록. 작업 이력을 중복 기록하지 않습니다. |
| `.mesh/work.log` | 기존 업무 일지가 없을 때만 만드는 기본 일지 |
| `.mesh/handoffs/` | 기본 기록만으로 부족한 상세 인계가 필요할 때 생성 |
| `AGENTS.md` | 공통 진입점. 기존 사용자 내용과 관리 블록을 구분합니다. |
| 필요한 제품별 안내 파일 | 실행 환경에 따라 `CLAUDE.md`, `GEMINI.md` 등의 연결을 보완합니다. |

업무 일지는 하나만 사용합니다. 명시적인 경로 변경 요청이 없으면 설정된 유효한 경로를 유지하고 추가 탐색을 끝냅니다. 설정이 없을 때 기존 일지를 찾아 활용합니다. 대상이 실제로 모호하면 설치 방식 선택과 함께 경로를 확인합니다.

## 작업 중 기록과 다음 작업의 재개

사용자는 본작업을 요청하면 됩니다. 통상적인 기록을 위해 별도 허락이나 저장 지시를 반복할 필요는 없습니다.

- 새 작업이나 재개 시 현재 상태와 관련 최근 기록을 읽고 이전 결정과 제약을 반영합니다.
- 관련 원본과 가능한 경우 Git 상태를 대조합니다. 기록의 다음 행동도 이번 요청 범위 안에서 실행합니다.
- 작업한 답변을 보내기 전에 새로 생긴 의미 있는 내용만 짧게 기록하고 저장을 확인합니다. 새 내용이 없으면 중복 기록하지 않습니다.
- 긴 작업에서는 중요한 결정, 단계 완료, 실패 원인 확인, 검증 결과 확보 시 중간 기록을 남깁니다. `/exit` 감지에 의존하지 않습니다.
- 일지는 시간순으로 추가하고 `STATE.md`는 현재 상태가 달라졌을 때만 갱신합니다.
- 변경 이유, 중요한 성공과 실패, 검증 방법과 범위, 미해결 문제, 다음 행동을 필요한 만큼 남깁니다. 상세 결과는 파일 경로로 연결합니다.
- 같은 작업에서 확인한 지침을 반복해서 읽지 않습니다. 문맥 압축, 인계, 관련 변경이나 불확실성이 생기면 다시 확인합니다.

짧은 추가 기록과 변경 부분 확인으로 부가 비용을 관리합니다. 전체 시간과 토큰이 얼마나 줄어드는지는 프로젝트와 활용 방식에 따라 달라지며, 비교 실측 결과는 아직 없습니다.

## 질문과 작업의 경계

정보, 상태, 의견, 평가, 비교 등을 묻는 요청은 필요한 범위만 읽고 답하며 기록을 변경하지 않습니다. 답변을 요약하거나 대화 내용을 목록으로 정리하라는 요청도 읽기 전용입니다. 파일 저장이나 기존 프로젝트 기록 갱신을 명시한 경우에만 해당 변경을 수행합니다. “삭제되어 있는가?”는 존재 여부 확인이며 삭제 명령으로 취급하지 않습니다.

프로젝트 파일이나 저장된 기록의 생성, 수정, 정리, 프로젝트 작업의 실행과 재개를 요청하면 해당 범위의 작업과 필요한 기록 갱신을 수행합니다. 답변 자체의 정리와 파일 또는 저장 기록의 정리를 구분합니다. 기록의 다음 행동이나 외부 스킬이 새로운 작업 권한을 부여하지는 않습니다.

실행 환경의 접근 제한은 준수합니다. 저장이 차단되면 가능한 작업을 계속하고 저장하지 못한 파일과 이유를 보고합니다. 승인 설정을 바꾸거나 제한을 우회하지 않습니다. 외부 배포와 Git 커밋, 푸시는 해당 작업의 허용 범위에 있을 때 수행합니다.

## 스킬은 필요한 상황에서 활용

에이전트가 작업에 맞춰 절차를 선택합니다. 기본 설치에서 전체 스킬 모음을 설치하거나 한꺼번에 읽지 않습니다. 기존의 호환 가능한 스킬을 우선 사용하고, 출처와 고정 개정 번호를 설정에 보관합니다.

| 절차 | 역할 |
|---|---|
| Ponytail | 기존 기능을 재사용하고 불필요한 구현을 줄입니다. 요청한 내용과 필요한 검증, 오류 처리, 기록을 유지합니다. |
| Graphify | 관계 파악이 어렵거나 유용한 그래프가 있을 때 구조적 지식을 정리하고 재활용합니다. 중요한 발견은 기존 기록에 연결합니다. |
| 완료 검증 | 완료 주장에 맞는 실제 근거와 범위를 확인합니다. 일지 저장마다 전체 검사를 반복하지 않습니다. |
| 문맥 압축 | 목적, 제약, 결정 이유, 정확한 경로, 실패, 검증 결과와 다음 행동을 보존한 요약을 만듭니다. 원본과 기존 이력을 유지합니다. |
| 체계적 오류 분석 | 오류를 재현하고 원인과 가설을 검증하며 유용한 시도 결과를 남깁니다. |
| 스킬 제작 | 반복해서 성공한 절차를 후보로 기록하고, 제작이 요청 범위에 포함될 때 재사용 가능한 스킬로 만듭니다. |

셋업의 간결한 운영 절차는 외부 스킬 원문과 구분합니다. 필요하면 고정된 출처 개정을 참고하고, 로컬에 복사할 때 라이선스와 필요한 참조 자원도 유지합니다. 사용자 요청, 원본 보존, 읽기 전용 질문과 접근 권한을 우선합니다.

Graphify는 별도 업무 일지를 만드는 용도가 아닙니다. 필요한 때 원본과 대조해 갱신하고, 자주 바뀌는 일지와 백업을 불필요하게 다시 분석하지 않습니다. 문서와 이미지의 의미 분석에는 토큰이 사용되며 코드 갱신과 비용이 다릅니다. [공식 비용 설명](https://docs.graphify.com/open-source/features/honest-token-savings)

부가 스킬, 실행 환경, 네트워크가 없어도 기본 읽기와 기록, 저장, 재개는 진행합니다. 감시 서비스, Git 훅, 데이터베이스, 공용 설정 변경은 기본 기록의 필수 조건이 아닙니다.

## 초기 적용부터 에이전트 호환성 확인

| 제품 | 연결 기준 |
|---|---|
| Codex | `AGENTS.md`와 관련 `AGENTS.override.md`의 우선순위를 확인합니다. [공식 문서](https://learn.chatgpt.com/docs/agent-configuration/agents-md) |
| Claude Code | 버전, 설정과 기존 `CLAUDE.md` 계열에 따라 `AGENTS.md` 로딩을 확인하고, 필요하면 `@AGENTS.md`를 구성합니다. [공식 문서](https://code.claude.com/docs/en/memory#agentsmd) |
| Antigravity | App, CLI, IDE의 실제 환경에서 `AGENTS.md`, `GEMINI.md` 등의 규칙을 확인합니다. Gemini CLI와 같은 제품으로 가정하지 않습니다. [공식 문서](https://antigravity.google/docs/rules/) |
| 그 밖의 에이전트 | 공통 파일 절차를 수행하고 확인된 프로젝트 지침 진입점에 연결합니다. 자동 로딩을 확인하지 못한 부분은 보고합니다. |

스킬 검색 경로와 호출 방식도 환경에 맞춥니다. 전용 명령이나 훅이 없는 환경에서는 운영 규칙을 직접 따라 작업합니다. 파일 생성 확인과 새 세션에서 지침이 실제로 불러와진 확인은 구분합니다.

## 배포와 개편 내역

공개 저장소에 올리는 파일은 다음 두 개입니다.

```text
README.md
memorymesh-setup.md
```

프로젝트별 기록, 백업, 스킬 캐시, 로컬 검증 자료는 배포에 포함하지 않습니다. 이 저장소의 로컬 추적과 커밋, 푸시 제한 및 사용자 단독 작성자 구성을 유지합니다. 공헌자나 공동 작성자 표기를 자동으로 추가하지 않습니다.

셋업, README, 릴리스 제목과 본문, 첨부 파일에는 사용자의 이메일, 실명, 연락처, 주소, 로컬 계정명과 개인 경로 등 개인 식별 정보를 포함하지 않습니다. 계정에 연결된 비공개 이메일 대체 주소도 문서에 넣지 않습니다. 예시는 중립적인 자리표시자와 프로젝트 기준 상대 경로를 사용합니다. 인증 정보, Git 작성자 설정과 로컬 검증 출력도 배포 문구에 복사하지 않습니다.

게시 전에는 실제 최종 내용을 점검합니다. 자동 패턴 검사는 직접 검토를 보조하며, 모든 개인 정보를 검출했다는 보장은 하지 않습니다.

릴리스 노트는 GitHub Releases 본문에 제공할 수 있습니다. 문서 개편만으로 릴리스를 게시하거나 원격에 푸시하지 않습니다.

### 2026. 11. 08. 개편

- 셋업을 영어로 작성하고 초기 적용 공통 절차와 제품별 연결을 구분했습니다.
- 설치 버전과 한 번의 방식 선택, 소유권 목록, 백업과 초기화 및 제거를 추가했습니다.
- 매 작업 답변 전 기록과 중간 저장, 이전 기록을 반영하는 재개를 구체화했습니다.
- 조건부 스킬 활용과 고정 출처, 부가 도구가 없을 때의 기본 동작을 정의했습니다.
- 중복 탐색과 반복 기록을 줄이고 검증 범위와 한계를 명시하도록 했습니다.

문서와 템플릿 검사만으로 모든 제품과 버전에서 초기 적용을 실행했다고 주장하지 않습니다. 실제 수행한 검증과 미검증 범위를 구분하며, 성능과 토큰 절감 수치는 별도 측정이 필요합니다.

버전 정보가 없거나 이전 Graphify 구성이 있는 설치는 기존 내용 유지 방식으로 확인할 수 있습니다. 공용 Graphify 패키지, 다른 용도의 그래프, 사용자 파일은 자동으로 제거하지 않습니다. 소유권이 불분명한 잔여 항목은 목록으로 보고합니다.

## English guide

MemoryMesh records useful work during execution and applies that evidence when work begins again. It supports sequential continuation across sessions and agents through one work log, concise current state, and detailed handoffs only when needed.

Copy memorymesh-setup.md to the project root and ask a file-capable agent:

```text
Apply memorymesh-setup.md to this project.
```

The setup is the normative execution procedure. Core file operations need no preinstalled plugin, skill, shell, Git, Python, or Node.js. The agent detects the host and existing installation, shows a concrete plan, and asks once for the applicable action: Install, Preserve and apply, Back up and reset, Remove, or Cancel. An explicit choice is not confirmed again.

Cancel stops immediately without backups or record changes. Remove performs only backup, removal, and removal checks; it does not create an installation first. Reset completes backup/archive before rebuilding. Install and Preserve and apply perform only their necessary setup steps. Compare date-based revisions by date and numeric suffix. An older setup is a downgrade candidate, not an upgrade, and may replace a newer installation only when that downgrade is explicitly selected in the same mode choice.

Preserve user instructions and history. Core records are CONFIG, PROTOCOL, STATE, an ownership manifest, and one log. An unchanged reapplication duplicates neither entries nor state. Reset/removal back up latest records outside .mesh and affect only identifiable owned resources/blocks. Shared tools and unrelated user content remain intact.

Before every final task response, save a concise delta for new meaningful work and confirm persistence; checkpoint during long tasks. Update STATE only when current information changes. Queries remain read-only. At a new task, consult relevant prior records, apply decisions and constraints, and verify relevant originals. Records retain the project's language and format.

Answer-only summaries, reformatted answers, and discussion lists remain read-only and create no record entry. Explicit requests to change project files, execute project operations, or save/update stored records are tasks. The word organize alone does not authorize file changes.

Conditional procedures cover Ponytail, Graphify, evidence-based completion, context compression, systematic debugging, and skill creation within scope. Reuse compatible installed skills and load only relevant instructions. Optional tools do not block core continuity. No universal performance or token-saving claim is made.

Adapters account for Codex overrides, Claude imports/loading behavior, and Antigravity rules and skill discovery. File creation, documented compatibility, and actual fresh-session execution are distinct validation levels. Report blocked saves and untested host behavior accurately.

Distribution and release content must exclude the user's email addresses, real name, contact details, personal identifiers, local account names, and private absolute paths. Use neutral placeholders and project-relative paths. Never copy account configuration, credentials, or private local logs into public documents. Review final content directly; pattern scans are supplementary.

Distribution remains exactly README.md and memorymesh-setup.md. Local records, backups, caches, and tests stay outside publication. Publish release notes in the GitHub Releases body only when a release is requested. This document revision does not itself push or publish.
