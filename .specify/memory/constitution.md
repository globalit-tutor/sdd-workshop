<!--
Sync Impact Report

Version change: none -> 1.0.0
Modified principles: Initial constitution created with 5 principles
Added sections: Additional Constraints, Development Workflow
Removed sections: placeholder tokens replaced
Templates requiring updates: .specify/templates/plan-template.md ✅, .specify/templates/spec-template.md ✅, .specify/templates/tasks-template.md ✅
Follow-up TODOs: none
-->

# 프로젝트 헌법 — ToDO CLI

## 핵심 원칙

### I. 레이어 분리 (Layer Separation)
비즈니스 로직은 사용자 인터페이스와 분리된 독립 레이어에서 구현되어야 한다. CLI(터미널) 입력/출력은 오직 인터페이스 레이어에서 처리하고, 도메인 로직과 서비스는 UI에 의존하지 않도록 설계한다.

- 규칙: `cli/`(또는 `bin/`)는 인터페이스 전용으로 유지하고, 핵심 로직은 `src/` 또는 `lib/`의 비-UI 레이어에 둔다.
- 검증: 레이어 경계는 단위 테스트로 검증되어야 하며, UI를 모킹(mock) 없이 비즈니스 로직을 테스트할 수 있어야 한다.

### II. 최소 의존성 (Minimal Dependencies)
외부 패키지 도입 전 그 필요성을 검토하고 문서화한다. 신규 의존성은 보안, 라이선스, 유지보수 비용 측면에서 정당화되어야 하며, 가능하면 표준 라이브러리 또는 이미 검증된 소수의 경량 패키지를 우선 사용한다.

- 규칙: PR에는 `Dependency justification` 섹션을 포함한다(대안 검토, 보안/라이선스 메모 포함).
- 검증: 의존성 추가는 코드 리뷰에서 명시적 승인을 받아야 한다.

### III. 테스트 우선 (Test-First, NON-NEGOTIABLE)
모든 기능은 테스트가 먼저 작성되어야 하며(단위/통합/행동 테스트), 테스트 없는 구현은 허용되지 않는다. TDD 워크플로우(레드-그린-리팩터)를 따르고, CI는 테스트 통과를 병합 전 필수로 요구한다.

- 규칙: 각 유저 스토리/기능은 실패하는 테스트를 먼저 포함하는 `tests/` 항목을 가져야 한다.
- 검증: PR은 관련 테스트가 포함되고 로컬/CI에서 실패-성공 순으로 검증된 경우에만 머지될 수 있다.

### IV. 지금 당장 필요하지 않은 추상화 금지 (No Premature Abstraction)
명확하고 직접적인 구현을 선호한다. 추상화는 실제 반복적 재사용 사례가 입증될 때만 도입한다. 불필요한 추상화는 코드 복잡도를 높이고 유지보수를 어렵게 한다.

- 규칙: 새로운 추상화(인터페이스/추상 클래스/패턴)는 적어도 두 번 이상의 실제 재사용 사례가 확인되었을 때만 도입한다. PR에는 `Abstraction rationale`를 포함해야 한다.
- 검증: 리팩터링으로 추상화를 도입할 경우, 성능·테스트·복잡성 영향 분석을 첨부한다.

### V. CLI 도구 구현 (CLI-Only Scope)
이 프로젝트의 목표는 터미널에서 사용하는 생산성 ToDO 관리 CLI 도구이다. REST API 서버나 GUI/웹 인터페이스는 기본적으로 프로젝트 범위 밖이며, 필요 시 별도 설계 문서와 명시적 승인을 거쳐 예외로 허용한다.

- 규칙: 모든 사용자 인터페이스는 표준 입력(stdin)/표준 출력(stdout)/표준 오류(stderr)를 사용한다. 출력은 사람용 포맷과 선택적 기계 판독용(JSON) 둘 다 지원할 수 있다.
- 검증: 새로운 기능이 네트워크/API/UI를 요구하면, 그 변경은 별도 이슈와 합의가 필요하다.

## 추가 제약사항 (Additional Constraints)

- 범위: CLI 전용, 네트워크 서버 및 GUI는 아웃 오브 스코프(예외는 명시적 승인 필요).
- 출력: 인간 가독성 우선, 필요 시 `--json` 플래그로 구조화된 출력 제공.
- 의존성 정책: 외부 의존성은 최소화, PR에 정당화 문구 포함.

## 개발 워크플로우 (Development Workflow)

- 흐름: Tests-first(TDD) → Implement → Code Review → Merge.
- CI: 모든 PR은 자동화된 테스트와 린트(lint)를 통과해야 한다.
- 코드 구조: `cli/`는 인터페이스 전용, `src/`/`lib/`는 비-UI 로직, `tests/`는 테스트 코드.

## 거버넌스 (Governance)

헌법 변경 절차:

- 제안: 변경은 PR로 제출하고 변경 사유와 영향을 문서화한다.
- 승인: 핵심 기여자 과반수의 승인(또는 프로젝트 규칙에 따른 투표)을 얻어야 한다.
- 마이그레이션: 기능적 영향이 있으면 마이그레이션 계획과 테스트를 포함해야 한다.

**Version**: 1.0.0 | **Ratified**: 2026-05-02 | **Last Amended**: 2026-05-02

