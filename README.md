# MemoryMesh Harness

MemoryMesh Harness는 AI 코딩 에이전트가 프로젝트의 작업 상태를 여러 세션과 에이전트 사이에서 이어받도록 돕는 파일 기반 작업 연속성 체계입니다.

Codex, Claude Code, Antigravity CLI, OpenCode 등 서로 다른 AI 코딩 도구를 번갈아 사용하거나, 하나의 도구를 여러 세션에 걸쳐 사용할 때 생기는 맥락 단절을 줄입니다. 별도의 서버나 데이터베이스 없이 프로젝트 폴더의 Markdown 파일로 작업 이력과 공유 정보를 관리하고, Graphify가 프로젝트 전체의 지식 그래프와 Markdown Wiki를 생성합니다.

작업 상태, 체크포인트와 핸드오프는 에이전트 종류와 관계없이 `.mesh/`에 기록합니다. Claude Code 메모리, Codex 세션 상태처럼 특정 에이전트의 private/internal 저장소는 프로젝트 작업 연속성의 기준으로 사용하지 않습니다.

## 무엇을 해결하는가

AI 코딩 에이전트를 오래 사용하거나 여러 도구를 함께 쓰면 다음 문제가 생기기 쉽습니다.

- 새 세션이 이전 작업의 목적, 변경 내용과 남은 일을 알지 못합니다.
- 다른 에이전트가 프로젝트를 처음부터 다시 분석합니다.
- 긴 작업에서 중간 상태, 결정 이유와 위험 요소가 사라집니다.
- 상태를 묻는 질문을 변경 명령으로 잘못 해석할 수 있습니다.
- 자동 분석 결과와 실제 작업 기록이 한곳에 섞입니다.
- 프로젝트 구조가 바뀌었는데도 오래된 분석 결과를 계속 참조할 수 있습니다.

MemoryMesh는 작업 연속성 기록과 자동 생성 프로젝트 지식을 분리하고, 에이전트가 필요한 정보만 순서대로 읽도록 안내합니다.

## 구성 요소

| 위치 | 역할 | 관리 방식 |
|---|---|---|
| `.mesh/` | 프로토콜, 프로젝트 개요, 작업 로그, 체크포인트, 핸드오프, 색인과 설치 스크립트 | 에이전트가 작업 중 생성하고 갱신 |
| `AGENTS.md` | 모든 에이전트의 공통 진입점 | MemoryMesh 관리 블록만 갱신 |
| `graphify-out/` | 프로젝트 지식 그래프, 분석 보고서, 시각화와 Markdown Wiki | Graphify가 생성 |

프로젝트의 실제 소스 파일과 문서가 최종 기준입니다. `graphify-out/`은 원본을 분석한 파생 자료이므로 중요한 변경 전에는 관련 원본 파일을 다시 확인해야 합니다.

## 설치 후 구조

```text
project/
├─ AGENTS.md
├─ CLAUDE.md
├─ GEMINI.md
├─ .graphifyignore
│
├─ .mesh/
│  ├─ PROTOCOL.md
│  ├─ OVERVIEW.md
│  ├─ work.log
│  ├─ INDEX.md
│  ├─ handoffs/
│  ├─ archive/
│  ├─ cold/
│  ├─ reports/
│  └─ scripts/
│     └─ install-graphify-agents.sh
│
└─ graphify-out/                 최초 Graphify 실행 후 생성
   ├─ graph.html
   ├─ GRAPH_REPORT.md
   ├─ graph.json
   └─ wiki/
      └─ index.md
```

셋업 단계에서는 `graphify-out/`을 만들지 않습니다. 설치가 끝난 뒤 현재 AI 코딩 어시스턴트 안에서 Graphify 스킬을 실행해야 생성됩니다.

## 주요 기능

### 작업 연속성

`.mesh/work.log`에 주요 작업, 변경 파일, 결정, 차단 요인과 그래프 상태를 시간순으로 기록합니다. 로그는 추가 전용이며 기존 항목을 고치지 않습니다. 잘못된 기록은 `CORRECTION` 이벤트로 정정합니다.

### 체크포인트

파일을 여러 개 수정하거나, 작업 단계가 길거나, 방향이 바뀌었거나, 다음 세션이 현재 상태를 복원하기 어려운 경우 체크포인트를 남깁니다. 체크포인트에는 완료한 일, 변경 파일, 현재 초점, 남은 일과 위험 요소가 포함됩니다.

### 작업 공유

다른 에이전트나 새 세션이 작업을 이어받아야 할 때 `.mesh/handoffs/`에 작업 공유 파일을 만듭니다. 이 파일은 내부적으로 핸드오프 기록으로 관리되며, 작업 요약, 결정, 현재 상태, 남은 일, 확인된 문제점과 정확한 다음 행동을 담습니다.

### 프로젝트 개요

`.mesh/OVERVIEW.md`에는 프로젝트 목적, 기술 스택, 핵심 구조, 확정된 결정, 현재 작업, 열린 핸드오프와 Graphify 상태를 짧게 유지합니다.

### 프로젝트 지식 그래프와 Wiki

Graphify는 프로젝트 파일을 분석해 다음 자료를 만듭니다.

- `GRAPH_REPORT.md`: 프로젝트 전체 개요, 주요 노드, 커뮤니티와 추천 질문
- `graph.json`: 관계 질의와 경로 분석에 사용하는 구조화 그래프
- `graph.html`: 브라우저에서 탐색하는 그래프 시각화
- `wiki/index.md`: 자동 생성 Markdown Wiki의 시작 문서

Graphify 결과는 작업 로그가 아닙니다. `.mesh/`의 로그나 핸드오프를 Graphify Wiki에 복사하지 않습니다.

## 사전 조건

기본 MemoryMesh 구조는 프로젝트 파일을 읽고 쓸 수 있는 AI 코딩 에이전트에서 사용할 수 있습니다.

Graphify 설치에는 다음 조건이 필요합니다.

- Python 3.10 이상
- `uv` 또는 `pipx`
- Python 패키지를 내려받을 네트워크 연결
- 사용자 범위 도구를 설치할 권한

셋업은 Graphify의 기본 패키지인 `graphifyy`만 설치합니다. OpenAI, Gemini, Ollama, Kimi 등의 선택적 백엔드 추가 패키지는 설치하지 않습니다.

## 설치 방법

### 1. 셋업 파일 배치

`memorymesh-setup.md`를 적용할 프로젝트의 최상위 폴더에 넣습니다.

```text
my-project/
├─ memorymesh-setup.md
├─ src/
└─ ...
```

### 2. AI 코딩 에이전트 실행

Codex, Claude Code, Antigravity CLI, OpenCode 등 사용할 AI 코딩 에이전트를 프로젝트 최상위 폴더에서 실행합니다.

### 3. 셋업 적용 요청

```text
memorymesh-setup.md 파일의 설정 내용을 이 폴더에 적용하라.
```

에이전트는 다음 작업을 수행합니다.

- 기존 MemoryMesh 파일과 프로젝트 지침 검사
- 최초 설치인지 기존 구조 재적용인지 판별
- `.mesh/` 작업 연속성 구조 생성 또는 갱신
- `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`의 MemoryMesh 관리 블록 생성 또는 갱신
- Graphify 기본 패키지의 사용자 범위 설치 확인
- `.mesh/scripts/install-graphify-agents.sh` 생성 또는 갱신
- 알려진 모든 AI 코딩 하네스에 Graphify 등록을 한 번에 수행
- `AGENTS.md`를 모든 에이전트의 공통 진입점으로 유지
- `.graphifyignore`의 MemoryMesh 관리 블록 생성 또는 갱신
- 설치 결과, 충돌과 차단 요인 검증 및 기록

기존 사용자 작성 내용은 덮어쓰지 않습니다. MemoryMesh 관리 블록만 갱신하며, 직접 충돌하는 기존 지침은 보존한 뒤 완료 보고에 표시합니다.

### 4. AI 코딩 에이전트 재시작

새로 등록한 스킬, 훅이나 갱신된 지침을 현재 세션이 바로 읽지 못할 수 있습니다. 셋업 완료 보고에서 재시작을 권하면 현재 AI 코딩 에이전트를 종료한 뒤 같은 프로젝트 폴더에서 다시 실행합니다. 전용 Graphify 훅이나 스킬이 없는 에이전트도 `AGENTS.md`를 통해 최소 사용 지침을 받습니다. `GEMINI.md`는 Antigravity 등 Google/Gemini 계열 도구와의 호환 지침 파일로 유지합니다.

### 5. 최초 그래프와 Wiki 생성

Claude Code, OpenCode, Antigravity와 호환 슬래시 명령 환경:

```text
/graphify . --wiki
```

Codex:

```text
$graphify . --wiki
```

이 명령은 `graphify-out/`에 프로젝트 지식 그래프, 분석 보고서, 시각화와 Markdown Wiki를 생성합니다.

일반 터미널에서 `graphify .`를 초기 생성의 기본 방법으로 사용하지 않습니다. 터미널 CLI는 헤드리스 추출 백엔드를 선택해 별도의 API 키를 요구할 수 있습니다.

## 기본 사용법

설치 뒤에는 평소처럼 작업을 요청합니다.

```text
일정표 파일을 최신화하라.
```

파일을 변경하는 작업을 시작하기 전에 에이전트는 다음 순서로 현재 맥락을 확인합니다.

1. `.mesh/PROTOCOL.md`
2. `.mesh/OVERVIEW.md`
3. `.mesh/work.log`의 최근 기록
4. 필요한 경우 `.mesh/handoffs/`와 `.mesh/INDEX.md`
5. 그래프가 있으면 범위를 좁힌 Graphify 질의 또는 생성된 Wiki
6. 실제 변경에 필요한 원본 프로젝트 파일

### 현재 상태 확인

```text
현재 프로젝트 진행 상태와 남은 작업을 설명하라.
```

상태, 진행률, 의견, 평가, 비교, 개수와 예 또는 아니요를 묻는 질문은 읽기 전용으로 처리합니다. 에이전트는 필요한 정보만 읽고 답한 뒤 멈춥니다.

### 작업 상태 기록

```text
현재까지 진행 상황을 정리하여 기록하라.
```

### 다른 에이전트나 세션과 작업 공유

```text
현재 작업을 마감하고 다른 에이전트가 이어받을 수 있도록 작업 상태를 정리하라.
```

새 에이전트에서는 다음처럼 요청합니다.

```text
이전 작업 기록을 확인하고 이어서 진행하라.
```

### Graphify를 이용한 구조 파악

```text
Graphify 보고서와 위키를 바탕으로 이 프로젝트의 주요 요소와 의존 관계를 설명하라.
```

특정 문제를 분석할 때는 범위를 좁힙니다.

```text
Graphify를 사용해 각 자료의 관계를 확인하고 관련 원본 파일을 확인하라.
```

Graphify 분석은 누락이나 잘못된 추론을 포함할 수 있습니다. 코드, 설정이나 문서를 실제로 수정하기 전에는 관련 원본 파일에서 확인합니다.

## 자동으로 반영되는 범위

MemoryMesh는 프로젝트 폴더를 계속 감시하는 백그라운드 서비스가 아닙니다. 파일이 생성, 변경 또는 삭제되었다고 해서 모든 변화가 즉시 `.mesh/`와 Graphify에 반영되지는 않습니다.

### 작업 중 자연스럽게 반영되는 내용

AI 코딩 에이전트가 MemoryMesh 프로토콜을 읽고 직접 작업하는 동안에는 다음 내용이 작업 과정에 따라 기록됩니다.

- 수행한 작업과 변경 파일
- 주요 결정과 제약
- 체크포인트
- 남은 작업과 위험 요소
- 다른 세션이나 에이전트와 공유할 작업 상태
- 프로젝트 구조 변경으로 인한 Graphify 그래프의 노후 가능성

에이전트가 직접 수행한 작업은 일반적으로 작업 종료 시 `.mesh/work.log`와 필요한 상태 파일에 반영됩니다.

### 자동으로 반영되지 않는 내용

다음 변경은 MemoryMesh가 자동으로 감지하거나 정리하지 않습니다.

- 사용자가 파일 탐색기에서 직접 추가, 변경 또는 삭제한 파일
- Git pull, 압축 해제, 동기화 프로그램 등 외부 도구가 변경한 파일
- Google Calendar, 이메일, 클라우드 문서 등 프로젝트 폴더 밖의 정보
- 기존 Graphify 그래프와 Wiki의 자동 재생성
- 오래된 로그의 자동 요약과 정리

외부 변경을 반영하려면 에이전트에게 명시적으로 요청합니다.

```text
현재 프로젝트 폴더의 변경 사항을 검사하고 기록하라.
```

또는 먼저 차이만 확인할 수 있습니다.

```text
프로젝트 폴더의 현재 상태와 로그 기록을 비교하고 누락되거나 오래된 내용을 보고하라.
```

첫 번째 요청은 파일을 갱신하는 Task mode입니다. 두 번째 요청은 결과만 보고하는 Query mode입니다.

## 새로운 파일과 일정 활용

프로젝트 폴더에 새 문서, 회의록, 설계 자료, 요구사항, 일정 파일이나 데이터 파일을 추가하면 에이전트가 필요할 때 해당 파일을 읽고 작업에 활용할 수 있습니다.

다만 파일이 존재한다는 이유만으로 `.mesh/OVERVIEW.md`, 작업 로그나 Graphify Wiki가 자동으로 갱신되지는 않습니다. 새 자료를 프로젝트 맥락에 반영하려면 다음처럼 요청합니다.

```text
새로 추가된 문서와 일정 파일을 검토하고 프로젝트 개요와 관련 작업 계획에 반영하라.
```

```text
최근 추가된 파일을 분석하고 기존 프로젝트 구조와 어떤 관계가 있는지 보고하라.
```

Google Calendar나 Gmail처럼 프로젝트 폴더 밖의 정보는 자동으로 가져오지 않습니다. 일정이나 메시지를 프로젝트 지식으로 사용하려면 지원되는 연결 기능으로 읽게 하거나, 파일로 내보내 프로젝트 폴더에 저장해야 합니다.

## Graphify 그래프 갱신

Graphify 그래프와 Wiki는 생성 당시의 프로젝트 상태를 반영합니다. 이후 프로젝트 파일이 바뀌어도 자동으로 다시 생성되지 않습니다.

다음과 같은 변경이 있었다면 다시 생성하는 것이 좋습니다.

- 주요 모듈 추가 또는 삭제
- 디렉터리 구조 변경
- API, 데이터베이스 스키마 또는 의존성 변경
- 핵심 문서의 대규모 수정
- 파일 간 관계에 영향을 주는 리팩터링

슬래시 명령 환경

```text
/graphify . --wiki
```

Codex:

```text
$graphify . --wiki
```

프로젝트 구조가 크게 바뀐 작업을 수행한 에이전트는 `.mesh/work.log`에 `GRAPH_STALE`을 기록할 수 있습니다. 이 기록은 그래프가 오래되었을 가능성은 알려주지만, 자동으로 다시 생성한다는 의미는 아닙니다.

## 실전 활용 예

### 기능 개발

```text
결제 승인 기능을 구현하고 테스트하라. 작업 중 주요 결정은 기록하고 완료 후 체크포인트를 남겨라.
```

### 버그 분석

```text
최근 로그인 실패 원인을 조사하라. Graphify로 관련 모듈을 좁힌 뒤 실제 소스 파일을 확인하고 수정하라.
```

### 코드 검토

```text
현재 열린 핸드오프를 읽고 이전 에이전트가 수정한 인증 코드를 검토하라. 문제점만 보고하고 파일은 변경하지 마라.
```

### 외부 변경 반영

```text
Git pull 이후 변경된 파일을 검사하고 MemoryMesh 개요와 최근 작업 상태를 갱신하라.
```

### 작업 중단 전 정리

```text
현재까지 완료한 일, 변경 파일, 남은 일과 문제점 등 주요 항목으로 기록하라.
```

### 다른 에이전트로 전환

```text
현재 작업을 다른 에이전트가 이어받을 수 있도록 작업 상태를 정리하라.
```

### 오래된 그래프 점검

```text
현재 Graphify 그래프가 프로젝트 상태와 일치하는지 확인하고, 다시 생성할 필요가 있는지만 보고하라.
```

## Obsidian 활용

만약 Obsidian을 사용한다면 프로젝트 루트를 Vault(보관함)로 열어 `.mesh/`와 `graphify-out/wiki/`를 함께 탐색할 수 있습니다. Graphify Wiki만 보고 싶다면 `graphify-out/wiki/`를 별도 Vault로 열 수 있습니다. MemoryMesh와 Graphify가 생성하는 자료는 일반 Markdown 파일이므로 VS Code, GitHub와 다른 편집기에서도 읽을 수 있습니다.

Obsidian은 다음 작업에 유용합니다.

- 프로젝트 개요, 체크포인트와 Wiki 문서를 한곳에서 열람
- Markdown 링크와 백링크 탐색
- 프로젝트 개념과 문서 관계 시각화
- 태그와 전체 검색으로 자료 탐색
- 여러 문서를 나란히 열어 비교

Graphify에서 Obsidian용 Vault를 생성하려면 다음 명령을 참고할 수 있습니다.

```text
/graphify . --obsidian
```

기존 Obsidian Vault나 원하는 폴더에 쓰려면 다음처럼 대상 폴더를 지정합니다.

```text
/graphify . --obsidian --obsidian-dir ~/vaults/my-project
```

Graphify 스킬은 `--obsidian`을 명시했을 때만 Obsidian Vault를 생성합니다. 기본 출력 위치는 `graphify-out/obsidian/`이며, `--obsidian-dir`을 지정하면 해당 폴더에 씁니다. 기존 Vault를 대상으로 할 때는 사용자의 기존 노트와 `.obsidian` 설정을 덮어쓰지 않는 방식으로 동작합니다.

터미널에서 이미 생성된 그래프를 Obsidian 형식으로 내보내야 할 때는 다음 CLI 명령도 사용할 수 있습니다.

```bash
graphify export obsidian
```

```bash
graphify export obsidian --dir ~/vaults/my-project
```

`graphify-out/wiki/`와 `graphify-out/obsidian/`은 자동 생성 자료입니다. 중요한 내용을 직접 고치기보다 원본 프로젝트 파일을 수정한 뒤 Graphify를 다시 실행하는 편이 좋습니다. 단, Graphify 재실행 시 수동 수정 내용이 사라질 수 있습니다.

MemoryMesh는 Obsidian 프로그램 자체나 플러그인, 별도 Vault 설정을 자동으로 설치하지 않습니다.

## 요청 처리 방식

### Query mode

정보, 상태, 진행률, 요약, 의견, 평가, 비교, 개수와 예 또는 아니요를 묻는 요청입니다.

```text
현재 열린 핸드오프는 몇 개인가?
그래프가 현재 프로젝트 상태와 일치하는가?
이 구조가 적절한가?
```

Query mode에서는 파일을 변경하지 않고, 로그를 추가하지 않으며, 도구를 설치하거나 실행하지 않습니다.

### Task mode

파일 생성, 수정, 삭제, 이동, 이름 변경, 프로그램 실행, 패키지 설치, 설정 변경이나 결과 생성을 명시적으로 요구하는 요청입니다.

```text
결제 모듈 오류를 수정하고 테스트하라.
Graphify 그래프와 Wiki를 다시 생성하라.
```

Task mode에서는 프로토콜과 현재 상태를 먼저 확인하고, 필요한 파일만 읽은 뒤 작업합니다.

## Graphify 설치 방식

Graphify의 공식 Python 패키지명은 `graphifyy`이고 설치 후 명령은 `graphify`입니다.

셋업은 기존 `graphify --version`이 정상 작동하면 설치된 버전을 유지합니다. 설치되지 않은 경우 다음 순서로 사용자 범위에 설치합니다.

```bash
uv tool install graphifyy
```

`uv`를 사용할 수 없으면 다음 명령을 사용합니다.

```bash
pipx install graphifyy
```

프로젝트 가상 환경이나 `pip install`은 사용하지 않습니다. 또한 `[openai]`, `[gemini]`, `[ollama]`, `[kimi]`, `[all]` 같은 선택적 백엔드 추가 패키지는 설치하지 않습니다.

설치 후에는 `.mesh/scripts/install-graphify-agents.sh`를 생성하고 프로젝트 루트에서 한 번 실행합니다. 이 스크립트는 Graphify의 범용 프로젝트 등록 명령을 우선 사용해, 어느 에이전트에서 셋업을 적용했든 다른 지원 에이전트에서 이어 사용할 수 있게 합니다.

```bash
.mesh/scripts/install-graphify-agents.sh
```

스크립트의 기본 등록 명령은 다음입니다.

```bash
graphify install --project
```

이 명령이 현재 Graphify가 지원하는 프로젝트 등록을 한 번에 처리합니다. 일부 등록은 해당 에이전트 앱을 실행하는 것이 아니라 프로젝트나 사용자 지침 파일을 미리 만들어 두는 방식일 수 있습니다. 따라서 Codex에서 셋업한 뒤 Claude Code로 열거나, Claude Code에서 셋업한 뒤 Codex나 Antigravity로 여는 식의 전환을 같은 원칙으로 처리합니다.

구버전 Graphify에서 `graphify install --project`를 지원하지 않을 때만 스크립트가 다음 fallback 등록을 사용합니다.

```bash
graphify install --platform claude       # Claude Code
graphify install --platform codex        # Codex
graphify install --platform opencode     # OpenCode
graphify install --platform agents       # Agents-compatible assistant
graphify install --platform antigravity  # Antigravity
```

나중에 새 에이전트를 지원하려면 먼저 Graphify 패키지를 최신화해 `graphify install --project`가 처리하도록 합니다. native 프로젝트 등록이 해당 에이전트를 아직 포함하지 않을 때만 `.mesh/scripts/install-graphify-agents.sh`와 `.mesh/PROTOCOL.md`에 fallback 등록 한 줄을 추가합니다. `AGENTS.md`는 모든 에이전트의 공통 진입점으로 유지합니다. 어떤 플랫폼 등록이 실패해도 이미 성공한 등록은 유지되며, 실패한 플랫폼만 완료 보고에서 확인된 문제점으로 표시합니다.

## 다시 적용하기

같은 프로젝트에 최신 `memorymesh-setup.md`를 다시 적용할 수 있습니다.

```text
memorymesh-setup.md 파일의 설정 내용을 이 폴더에 다시 적용하라.
```

재적용할 때는 다음 원칙을 따릅니다.

- 기존 사용자 파일과 작성 내용을 보존합니다.
- MemoryMesh 관리 블록만 갱신합니다.
- 기존 로그와 핸드오프를 유지합니다.
- 빠진 폴더와 파일만 추가합니다.
- Graphify 설치와 all-agent 프로젝트 등록 상태를 다시 확인합니다.
- 기존 Graphify 그래프와 Wiki를 자동으로 다시 만들지 않습니다.
- 충돌하는 사용자 지침은 보존하고 보고합니다.

## Git과 보안

MemoryMesh 파일에는 프로젝트 맥락, 작업 이력과 결정이 포함될 수 있습니다. 공개 저장소에서는 커밋 전에 민감한 정보가 없는지 확인해야 합니다.

다음 항목의 버전 관리 여부는 프로젝트 성격에 따라 결정합니다.

- `.mesh/`
- Graphify가 설치한 프로젝트 지침
- `graphify-out/`

여러 사람이 작업 상태를 공유한다면 `.mesh/`를 버전 관리할 수 있습니다. 개인 작업 기록이나 민감한 프로젝트라면 저장소에서 제외하는 편이 안전합니다.

비밀번호, API 키, 토큰, 세션 쿠키와 불필요한 개인 정보는 `.mesh/`나 Graphify 관련 파일에 기록하지 않습니다.

## 문제 해결

### Graphify 설치가 완료되지 않은 경우

Python 3.10 이상, `uv` 또는 `pipx`, 네트워크나 설치 권한이 없으면 MemoryMesh 구조는 만들어지지만 Graphify 설정만 완료되지 않을 수 있습니다.

환경을 준비한 뒤 셋업 파일을 다시 적용합니다.

### `graphify` 명령을 찾지 못하는 경우

사용자 도구 경로가 현재 터미널에 반영되지 않았을 수 있습니다. AI 코딩 CLI나 터미널을 종료한 뒤 다시 실행합니다.

### 일부 에이전트 등록이 실패한 경우

특정 에이전트만 설치된 시스템에서 다른 에이전트 등록을 시도해도, 성공한 등록은 그대로 유지됩니다. 실패한 항목은 완료 보고의 확인된 문제점으로 보고되며, 나중에 해당 에이전트 환경을 준비한 뒤 `.mesh/scripts/install-graphify-agents.sh`를 다시 실행하면 됩니다.

### Graphify 스킬을 인식하지 못하는 경우

먼저 셋업이 만든 `.mesh/scripts/install-graphify-agents.sh`가 실행되었는지 확인합니다. 이후 AI 코딩 에이전트를 종료하고 같은 프로젝트 폴더에서 다시 시작합니다. 전용 스킬이나 훅을 인식하지 못하는 에이전트는 `AGENTS.md`의 공통 지침을 기준으로 작업합니다.

재시작 뒤 현재 환경에 맞는 `/graphify . --wiki` 또는 `$graphify . --wiki`를 실행합니다.

### 터미널 실행에서 API 키 오류가 발생하는 경우

터미널의 `graphify .`는 헤드리스 추출 백엔드를 사용할 수 있으며 OpenAI나 Gemini API 키를 요구할 수 있습니다. 초기 그래프 생성은 현재 AI 코딩 어시스턴트 안에서 Graphify 스킬 명령으로 실행합니다.

### 기존 지침과 충돌하는 경우

MemoryMesh는 기존 `AGENTS.md`, `CLAUDE.md`, `GEMINI.md` 내용을 삭제하지 않습니다. 직접 충돌하는 지침은 보존한 상태로 완료 보고에 표시합니다. `AGENTS.md`는 모든 에이전트의 공통 진입점이므로 충돌 여부를 특히 보수적으로 확인합니다.

### 그래프가 오래된 경우

프로젝트 구조, 주요 모듈, 의존성이나 핵심 문서가 변경된 뒤 Graphify 스킬을 다시 실행합니다. 자동 Git 훅이나 백그라운드 재생성은 기본 셋업에 포함되지 않습니다.

## 제한 사항

MemoryMesh는 파일 기반 운영 규칙입니다. 에이전트가 지침을 항상 완벽하게 따르도록 기술적으로 강제하지는 않습니다.

- 에이전트마다 지침 파일, 훅과 스킬 지원 수준이 다를 수 있습니다. 전용 통합이 없어도 `AGENTS.md`를 공통 진입점으로 사용합니다.
- 대규모 프로젝트에서는 Graphify 최초 분석에 시간이 걸릴 수 있습니다.
- 자동 생성 그래프와 Wiki에는 누락이나 잘못 추론된 관계가 포함될 수 있습니다.
- 프로젝트 폴더 밖의 일정, 이메일과 클라우드 문서는 자동으로 동기화되지 않습니다.
- 여러 에이전트가 동시에 같은 로그나 지침 파일을 수정하면 충돌할 수 있습니다.
- 생성된 Wiki를 직접 수정하면 Graphify 재실행 때 변경 내용이 사라질 수 있습니다.

중요한 코드 변경, 설정 변경과 프로젝트 판단은 실제 원본 파일과 사람이 검토하는 것이 좋습니다.

## 배포 파일

MemoryMesh Harness의 기본 배포 파일은 다음과 같습니다.

```text
README.md
memorymesh-setup.md
```

사용자는 `memorymesh-setup.md`를 대상 프로젝트 폴더에 복사하고 에이전트에게 적용을 지시합니다.

```text
memorymesh-setup.md 파일의 설정 내용을 현재 작업 폴더에 적용하라.
```

설치가 끝난 뒤 셋업 파일은 보관하거나 삭제할 수 있습니다. 향후 업데이트를 적용하려면 최신 셋업 파일을 다시 배치하고 재적용합니다.

사용 중 프로젝트 폴더(작업 폴더)에 궁금한 폴더나 파일이 있다면 사용 중인 에이전트에 직접 질문하여 확인하는 것도 좋은 방법입니다.

## 관련 자료

- [Graphify 공식 저장소](https://github.com/safishamsi/graphify)
