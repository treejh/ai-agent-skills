# code-review 플러그인

PR을 5개의 병렬 에이전트가 독립적으로 검토하고, 신뢰도 점수로 오탐을 걸러내는 자동 코드 리뷰 플러그인.

> 출처: [claude-plugins-official](https://github.com/anthropics/claude-code) — 제작: Boris Cherny (Anthropic)  
> 버전: 1.0.0

---

## 설치

```bash
claude plugin install code-review@claude-plugins-official
```

---

## 사용법

PR 브랜치에서 실행:

```bash
/code-review
```

---

## 동작 원리

### 1단계: 사전 조건 확인 (Haiku)

다음 조건이면 자동 스킵:
- PR이 이미 닫혀 있음
- Draft 상태
- 자동화 PR이거나 변경이 사소함
- 이미 본인이 리뷰한 PR

### 2단계: 컨텍스트 수집 (Haiku 2개)

- 레포의 관련 `CLAUDE.md` 파일 목록 수집
- PR 변경 내용 요약

### 3단계: 5개 병렬 에이전트 리뷰 (Sonnet)

| 에이전트 | 역할 |
|---------|------|
| #1 | CLAUDE.md 가이드라인 준수 여부 |
| #2 | 변경된 코드의 명백한 버그 탐지 |
| #3 | git blame/히스토리 맥락 기반 이슈 |
| #4 | 같은 파일 건드린 이전 PR 코멘트 참고 |
| #5 | 코드 내 주석 가이드라인 준수 여부 |

### 4단계: 신뢰도 채점 (Haiku — 이슈당 병렬)

각 이슈를 0-100으로 독립 채점:

| 점수 | 의미 |
|-----|------|
| 0 | 오탐 — 검토에 안 버팀 |
| 25 | 불확실 — 실제 이슈일 수도 |
| 50 | 보통 확신 — 사소한 문제 |
| 75 | 높은 확신 — 실제로 영향 있는 문제 |
| 100 | 확실 — 실제 버그, 빈번히 발생 |

**80점 미만은 전부 필터링.** 리뷰 코멘트는 80점 이상 이슈만 포함.

### 5단계: GitHub 리뷰 코멘트 게시

```markdown
### Code review

Found 2 issues:

1. Missing error handling (CLAUDE.md says "Always handle OAuth errors")
https://github.com/owner/repo/blob/[full-sha]/src/auth.ts#L67-L72

2. Memory leak: state not cleaned up (bug due to missing finally block)
https://github.com/owner/repo/blob/[full-sha]/src/auth.ts#L88-L95
```

---

## 자동 스킵되는 케이스

- 기존에 있던 이슈 (PR에서 새로 도입한 문제만 검토)
- 타입에러, 포맷, 린터가 잡는 문제 (CI에서 처리)
- 테스트 커버리지, 문서 부족 등 일반적 품질 문제 (CLAUDE.md 명시 없으면)
- `// eslint-disable` 같은 무시 주석이 달린 이슈
- 의도적으로 보이는 동작 변경

---

## 요구사항

- GitHub 저장소
- `gh` CLI 설치 및 인증 (`gh auth login`)
- CLAUDE.md (선택이지만 있으면 리뷰 품질 크게 향상)

---

## 다른 스킬과의 조합

| 조합 | 효과 |
|------|------|
| `check` → `code-review` | 로컬 diff 리뷰 후 PR 올려서 자동 PR 리뷰까지 |
| `test-driven-development` → `/ccm` → `/cpr` → `/code-review` | TDD 구현 → 커밋 → PR → 자동 리뷰 완전 자동화 |
| `writing-plans` → 구현 → `/code-review` | 계획대로 구현됐는지 CLAUDE.md 기준으로 검증 |

---

## 추천 워크플로우

### 기본 PR 리뷰

```
코드 작성
  ↓
/check           ← 로컬에서 먼저 diff 기반 리뷰
  ↓
/ccm             ← Conventional Commit
  ↓
/cpr             ← PR 생성
  ↓
/code-review     ← 5개 에이전트 자동 리뷰 + 신뢰도 필터링
```

### CLAUDE.md 기반 팀 가이드라인 적용

```
CLAUDE.md에 팀 컨벤션 명시
  ↓
/code-review 실행 시 자동으로 컨벤션 준수 여부 검사
  ↓
반복 패턴 발견 시 → CLAUDE.md 업데이트
```

---

## check 스킬과의 차이

| | `check` (스킬) | `code-review` (플러그인) |
|--|---------------|------------------------|
| 대상 | 로컬 diff | GitHub PR |
| 에이전트 | 보안 + 아키텍처 2종 | 5종 (버그·CLAUDE.md·히스토리·코멘트·이전PR) |
| 결과 | 로컬 출력 + 자동 수정 | GitHub PR 코멘트 게시 |
| 신뢰도 필터 | 없음 | 80점 미만 자동 제거 |
| 사용 시점 | 커밋/머지 전 로컬 | PR 올린 후 GitHub에서 |
