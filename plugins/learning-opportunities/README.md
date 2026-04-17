# learning-opportunities + orient 플러그인

AI 코딩 도구를 쓰면서도 **실제 실력이 늘도록** 설계된 학습 과학 기반 플러그인 쌍.

> 출처: [DrCatHicks/learning-opportunities](https://github.com/DrCatHicks/learning-opportunities) (CC-BY-4.0)  
> 제작: Dr. Cat Hicks (learning-opportunities), Dr. Michael Mullarkey (orient)

---

## 플러그인 구성

이 두 플러그인은 함께 사용하도록 설계되어 있다.

| 플러그인 | 역할 |
|---------|------|
| `learning-opportunities` | AI 코딩 작업 후 능동 학습 연습 제공 |
| `orient` | 레포별 `orientation.md` 생성 (학습 연습 재료) |

---

## 설치

```bash
# 두 플러그인을 모두 설치
claude plugin install learning-opportunities@learning-opportunities
claude plugin install orient@learning-opportunities
```

---

## learning-opportunities

### 언제 발동하나

아키텍처 작업(신규 파일, 스키마 변경, 리팩토링) 완료 후 자동 제안.  
직접 호출도 가능.

### 호출 방법

```
/learning-opportunities           ← 기본 학습 연습 (현재 작업 기반)
/learning-opportunities orient    ← orientation.md 기반 레포 온보딩 연습
```

### 어떻게 작동하나

1. 현재 작업(새 파일 생성, DB 스키마 변경, 리팩토링 등)이 끝나면 학습 연습을 제안
2. 10-15분짜리 인터랙티브 연습 제시
3. 매 질문마다 **먼저 답변을 기다린 후** 피드백 제공 (힌트 없음)
4. 틀린 예측 = 가장 가치 있는 학습 데이터로 활용

### 연습 유형

| 연습 | 설명 |
|------|------|
| **예측 → 관찰 → 반성** | 어떤 일이 일어날지 예측 후 실제 동작과 비교 |
| **직접 생성 → 비교** | 내 방식으로 먼저 구현해보고 실제 코드와 비교 |
| **경로 추적** | 요청이 코드를 통과하는 흐름을 단계별로 설명 |
| **버그 찾기** | 제시된 버그의 원인과 수정 방법 예측 |
| **다시 가르치기** | 신규 개발자에게 설명하듯 컴포넌트 설명 |
| **기억 확인** | 이전 세션의 컴포넌트 동작 회상 |

---

## orient

레포의 구조를 분석해서 `/learning-opportunities orient`가 사용할 `orientation.md`를 생성한다.

### 호출 방법

```
/orient:orient           ← 기본 분석 (README, 구조, 엔트리포인트, 테스트 읽기)
/orient:orient showboat  ← showboat CLI로 선형 코드 워크스루 문서 생성
```

### 어떻게 작동하나

1. 레포의 주요 언어 감지 (Python, TypeScript, Go 등)
2. README, 디렉토리 트리, 엔트리포인트, 테스트, 핵심 파일 분석
3. git 히스토리에서 고빈도 변경 파일 파악
4. 다음 위치에 `orientation.md` 작성:  
   `.claude/skills/learning-opportunities/resources/orientation.md`
5. 파일에는 핵심 개념, 공통 함정, 연습 시퀀스(2개) 포함

### 생성되는 파일 구조

```markdown
# Repo Orientation: [레포명]
## One-line purpose
## Primary language(s)
## Pipeline / workflow stages
## Key files
## Core concepts
## Common gotchas
## Suggested exercise sequence   ← 학습 연습 2개
```

---

## 추천 사용 패턴

### 새 레포 온보딩

```
/orient:orient                    ← orientation.md 생성 (~30초)
  ↓
/learning-opportunities orient    ← 레포 구조 학습 연습 (10-15분)
```

### 구현 후 학습

```
기능 구현 (새 파일, DB 변경 등)
  ↓
/learning-opportunities           ← 방금 만든 코드로 학습 연습
```

### 세션 시작 시 기억 리프레시

```
/learning-opportunities           ← 이전 세션 내용 retrieval check-in
```

---

## 다른 스킬과의 조합

| 조합 | 효과 |
|------|------|
| `think` → `learning-opportunities` | 설계 결정 이해도 검증 |
| `test-driven-development` → `learning-opportunities` | TDD 패턴 능동 학습 |
| `orient` → `learning-opportunities orient` → 개발 | 레포 온보딩 후 즉시 학습 |
| `session-wrap` + `learning-opportunities` | 세션 돌아보기 + 능동 복습 |

---

## 주의사항

- 세션 내 연습은 최대 2번 자동 제안 (그 이후는 명시적 호출 필요)
- 거절하면 같은 세션에서 다시 제안하지 않음
- `orient`는 자동 트리거 없음 — 항상 명시적 호출 필요
- `orientation.md`는 `.claude/skills/`에 저장되므로 git에 커밋 가능 (팀 공유 가능)
