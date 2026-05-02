# Feature Specification: ToDO CLI

**Feature Branch**: `001-todo-cli`
**Created**: 2026-05-02
**Status**: Draft
**Input**: User description: "CLI기반의 ToDO 앱을 만들고 싶어. 대상 사용자: 터미널에서 사용하는 개인 개발자. 주요기능: 1) ToDO 항목 추가: 제목(필수), 마감일(선택), 우선순위(선택) 2) 전체 목록 조회: 완료/미완료/우선순위로 필터링 가능 3) 항목 완료 처리: 항목 ID로 완료 표시 4) 항목 삭제 : 항목 ID로 삭제. 단, 기술 스택은 아직 미정"

> 헌법 제약: 본 프로젝트는 CLI 전용입니다. REST API나 GUI는 범위 밖입니다. 모든 기능은 Test-First(TDD)를 따라야 하며, 관련 테스트를 스펙에 포함해야 합니다.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Add ToDO (Priority: P1)

사용자는 새로운 ToDO 항목을 추가할 수 있다. 최소 입력은 `제목`이며, `마감일(due date)`과 `우선순위(priority)`는 선택 항목이다.

**Why this priority**: 핵심 사용자 가치(작업 기록과 추적) 제공.

**Independent Test**: CLI에서 `todo add "Write doc" --due 2026-05-10 --priority high` 실행 시 새로운 항목이 생성되고 목록 조회에서 확인 가능해야 한다.

**Acceptance Scenarios**:
1. Given 빈 목록, When `todo add "Title"`, Then 항목이 생성되고 `id`가 반환된다.
2. Given 항목이 존재할 때, When `todo add "Title" --due YYYY-MM-DD --priority low`, Then 항목에 `due_date`와 `priority`가 저장된다.

---

### User Story 2 - List & Filter (Priority: P1)

사용자는 전체 항목을 조회하고, `--completed`, `--incomplete`, `--priority <level>` 등으로 필터링할 수 있다.

**Why this priority**: 사용자는 현재 할 일 목록을 확인하고 우선순위에 따라 작업을 정렬/필터할 필요가 있음.

**Independent Test**: 여러 항목이 존재할 때 `todo list --incomplete --priority high`가 해당 조건을 충족하는 항목만 반환해야 한다.

**Acceptance Scenarios**:
1. Given 여러 항목, When `todo list --completed`, Then 완료된 항목만 출력된다.
2. Given 여러 항목, When `todo list --priority high`, Then 우선순위 high 항목만 출력된다.

---

### User Story 3 - Mark Complete (Priority: P2)

사용자는 항목의 `id`로 해당 항목을 완료 처리할 수 있다.

**Why this priority**: 상태 업데이트(완료)는 기본 작업 흐름의 일부이다.

**Independent Test**: `todo complete <id>` 실행 시 해당 항목의 `completed` 필드가 참으로 설정되어야 한다.

**Acceptance Scenarios**:
1. Given 항목이 `completed=false`, When `todo complete 5`, Then 항목 5의 `completed`가 true로 변경된다.

---

### User Story 4 - Delete Item (Priority: P3)

사용자는 항목의 `id`로 항목을 삭제할 수 있다.

**Why this priority**: 정리와 관리에 필요하지만 생성/조회/완료보다 우선순위가 낮음.

**Independent Test**: `todo delete <id>` 실행 시 항목이 목록에서 제거되어 더 이상 조회되지 않아야 한다.

**Acceptance Scenarios**:
1. Given 항목이 존재할 때, When `todo delete 3`, Then 항목 3이 영구 삭제된다.

---

### Edge Cases

- 잘못된 `id`가 주어졌을 때 적절한 오류 메시지와 비영향 동작 유지.
- 동일 제목으로 여러 항목을 추가할 수 있어야 하며, 식별은 `id`로 유지.
- 날짜 형식 오류는 사용자에게 명확히 안내되어야 한다.
- 로컬 저장소 손상 시 복구 또는 손상 방지 전략(백업/임시 파일)을 고려해야 한다.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: 시스템은 사용자가 `todo add <title> [--due YYYY-MM-DD] [--priority <low|medium|high>]`로 항목을 추가할 수 있어야 한다.
- **FR-002**: 시스템은 `todo list [--completed|--incomplete] [--priority <level>]`로 항목을 필터링 및 조회할 수 있어야 한다.
- **FR-003**: 시스템은 `todo complete <id>` 명령으로 항목을 완료 처리할 수 있어야 한다.
- **FR-004**: 시스템은 `todo delete <id>` 명령으로 항목을 삭제할 수 있어야 한다.
- **FR-005**: 각 명령은 적절한 종료 코드와 사용자 친화적 메시지를 반환해야 한다.
- **FR-006**: 모든 기능은 자동화된 테스트로 검증되어야 한다 (TDD 원칙 준수).

### Key Entities

- **TodoItem**: ToDO 항목을 나타냄
  - `id`: 정수 또는 UUID (식별자)
  - `title`: 문자열 (필수)
  - `due_date`: ISO 날짜 문자열 (선택)
  - `priority`: enum (`low`,`medium`,`high`) (선택)
  - `completed`: boolean
  - `created_at`, `updated_at`: 타임스탬프

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 사용자는 `todo add`로 항목을 추가 후 5초 이내에 목록에서 해당 항목을 확인할 수 있다.
- **SC-002**: 필터 명령(`--completed`, `--priority`)은 100개 이하 로컬 항목에 대해 1초 이내에 결과를 반환한다.
- **SC-003**: 95% 이상의 테스트 케이스가 CI에서 통과해야 하며, 모든 핵심 기능에 대해 자동화된 테스트가 존재해야 한다.
- **SC-004**: 사용자가 `todo complete <id>` 실행 후 항목 상태가 즉시 반영되어야 한다.

## Assumptions

- 프로젝트 범위는 CLI 전용이며, 네트워크 기반 REST API나 GUI는 아웃 오브 스코프이다.
- 기본 로컬 저장소는 파일 기반(예: JSON 또는 소형 로컬 DB)으로 가정하나, 기술 스택은 미정이며 설계 단계에서 결정한다.
- 사용자 환경은 단일 개발자 로컬 머신(오프라인 가능)을 가정한다.
- 시간대(Timezone) 처리는 로컬 시스템 시간대를 따르되, ISO 표기 사용을 권장한다.

***

**Spec Ready**: 본 스펙은 Plan 단계로 넘기기 위한 최소 요구를 포함하며, Test-First 원칙에 따라 각 유저 스토리의 실패하는 테스트(단위/통합)를 구현 문서에 연결해야 합니다.
