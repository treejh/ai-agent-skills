---
name: cobsidian
description: Analyze today's git commits and session conversation, then save categorized markdown files to ~/devlog/{repo-name}/{date}/. Based on cnotion templates but saves locally instead of Notion. All output in Korean.
version: 1.0.0
---

# cobsidian — Daily Work → Local Devlog (~/devlog)

## Purpose

Analyze today's work and save categorized markdown pages to `~/devlog/{repo-name}/{date}/`.
Write in **engineering blog style** — natural prose paragraphs, not bullet-point lists.
All output content is written in **Korean**.

---

## Config

> **Setup required**: Customize the values below for your environment.
> See [README.md](./README.md) for setup instructions.

- **Base path**: `~/devlog/`
- **Author filter**: `treejh` / `jihyun3066@naver.com` — ONLY process this author's commits
- **Active projects** (for scheduled runs):
  - `/Users/smplanet/spring_project/flat/Fermata-BE`
  - `/Users/smplanet/spring_project/route/project/backend/easyplus_en_server`

> When running as a scheduled task (non-interactive), process ALL active projects above.
> When run manually in a project directory, process only the current directory.

---

## Execution Steps

### STEP 1: Collect Today's Work

#### 1-1. Determine repo name and date

```bash
# repo name = basename of git root
basename $(git rev-parse --show-toplevel)

# output dir
# ~/devlog/{repo-name}/{YYYY-MM-DD}/
```

#### 1-2. Gather today's commits (author-filtered)

```bash
git log --oneline --since="YYYY-MM-DD 00:00:00" --author="treejh" --all

# If none, get recent commits
git log --format="%H %ad %s" --date=format:"%Y-%m-%d %H:%M" --author="treejh" -15
```

#### 1-3. Inspect each commit

```bash
git show --stat <hash>
git show <hash>
```

Read key changed files if needed.

#### 1-4. Check uncommitted changes

```bash
git status
git diff --stat
```

#### 1-5. Analyze session conversation (Critical)

Extract from the current Codex session:
- Concepts learned during implementation
- Design deliberations (A vs B — why we chose B)
- Misconceptions corrected
- Patterns or structures discovered in code
- Understanding gained from error messages

**These do NOT appear in commits but are the core material for Insight and Troubleshooting pages.**

---

### STEP 2: Derive Category-Based Work Items

Group multiple commits into one item if they belong to the same topic.

**Each item = 1 markdown file**

| Tag | Filename prefix | Criteria |
|-----|----------------|----------|
| 기능구현 | `기능구현-{title}.md` | New feature / API / logic |
| 트러블슈팅 | `트러블슈팅-{title}.md` | Bug found → root cause → fix |
| 인프라 | `인프라-{title}.md` | CI/CD, Docker, GitHub Actions, AWS |
| 리팩토링 | `리팩토링-{title}.md` | Code structure improvement |
| 인사이트 | `인사이트.md` | Lessons learned (ALWAYS create) |
| 다음과제 | `다음과제.md` | Unresolved issues, next steps (ALWAYS create) |

Title in filename: short Korean or English keyword, no spaces (use `-` instead).

---

### STEP 3: Draft Page Body — Engineering Document Quality

**⚠️ Drafts only. Do NOT write files yet. STEP 5 validation is mandatory first.**

**Core principle: Every page MUST include "Why did we do this → What did we choose → Why that choice → How did the outcome change."**

---

#### 🔥 트러블슈팅 파일

```markdown
# {제목}

{도입 단락}
어떤 증상이 나타났는지, 처음에 무엇을 의심했는지.
실제 원인은 달랐다는 흐름으로 시작.

## 문제 정의

어떤 상황에서 어떤 증상이 발생했는지.
단순히 "에러 발생"이 아닌 운영 영향까지 서술.

## 근본 원인 분석

표면 증상이 아닌 근본 원인 설명.
관련 기술 레이어가 왜 이 문제를 만드는지 포함.

```java
// Before — 문제 코드
```

## 설계 결정 및 트레이드오프

여러 해결책이 있었다면, 왜 이것을 선택했는지.
거부한 대안과 이유 포함.

## 해결 과정

무엇을 어떻게 변경했는지.

```java
// Before

// After
```

## 개선 효과 (Before / After)

수치나 운영 행동의 구체적 변화.

## 재발 방지 / 주의사항

비슷한 상황에서 먼저 의심할 것.
이 변경으로 생긴 사이드이펙트.
```

---

#### 🚀 기능구현 파일

```markdown
# {제목}

{도입 단락}
이 기능이 없었을 때 어떤 구체적 문제가 있었는지.

## 문제 정의

이 기능 없이 운영 상황 서술.

## 설계 결정

어디에 무엇을 추가했고, 왜 그 위치인지.

## 트레이드오프

다른 구현 위치/방법과 비교.

## 구현 내용

무엇을 추가/변경했는지.

```java
// 핵심 코드
```

## 운영 효과 (Before / After)

이 기능 후 운영에서 무엇이 달라졌는지. "개선됨"이 아닌 구체적 서술.
```

---

#### 🏗️ 인프라 파일

```markdown
# {제목}

{도입 단락}
어떤 운영 고통이나 리스크가 있었는지.

## 문제 정의

기존 인프라/설정의 어떤 부분이 문제였는지.

## 설계 결정 및 트레이드오프

어떻게 설계했고, 대안과 비교.

## 운영 효과

변경 후 운영 경험의 구체적 변화.
```

---

#### 💡 인사이트 파일 (ALWAYS create)

```markdown
# 인사이트 — {YYYY-MM-DD}

{도입 단락}
오늘 어떤 문제를 통해 어떤 새로운 이해를 얻었는지 1-2문장 요약.

## 새롭게 이해한 기술 개념

각 개념을 독립 단락으로.
순서: "이 개념이 왜 존재하는가 → 어떻게 동작하는가 → 언제 문제가 되는가"

## 설계 판단력이 생긴 것

다음에 비슷한 선택이 올 때 어떻게 결정할지.
형식: "A 상황에서는 B를 선택한다, C이기 때문에"

## 삽질에서 배운 것

처음에 무엇을 오해했고 어떻게 수정됐는지.
스토리 형식: "X라고 생각해서 Y를 했는데, 사실은 Z였다 — 왜냐하면..."

## 다음 번엔 이렇게 접근하자

이 경험을 바탕으로 비슷한 상황에서 먼저 확인할 것.
```

---

#### 🤔 다음과제 파일 (ALWAYS create)

```markdown
# 다음 과제 — {YYYY-MM-DD}

{도입 단락}
오늘 작업에서 미해결로 남거나 찜찜한 부분의 배경.

## 아직 해결 안 된 문제

미해결 이슈와 왜 오늘 해결 못 했는지 맥락.
TODO 목록이 아닌, "이게 해결 안 되면 어떤 운영 리스크가 있는지" 포함.

## 동작은 하지만 개선이 필요한 부분

현재 구현의 한계나 더 나은 방향.
지금 당장 바꾸지 않은 이유 포함.

## 다음 작업에서 검증해야 할 것

오늘 변경이 실제로 의도대로 동작하는지 확인 포인트.
```

---

### Writing Rules

- 자연스러운 한국어로 작성. 기술적 정확성이 최우선.
- 번역체 금지 — 한국 백엔드 개발자가 실제로 쓰는 표현으로.
- 추상적 서술 금지 — 모든 문장은 구체적 상황/수치/코드와 연결.
- "개선됨", "해결됨"으로 끝내지 말 것 — 무엇이 어떻게 바뀌었는지 명시.
- 수치화 우선: 200ms → 80ms, 10분 수동 검색 → 10초 grep 등.
- 수치 없으면 운영 행동 변화로: "SSH 직접 접속 → CloudWatch 즉시 조회"

---

### STEP 4: Korean Encoding Rule

모든 한국어는 리터럴 문자로. 유니코드 이스케이프(`\uXXXX`) 절대 금지.

---

### STEP 5: Korean Language Validation (MANDATORY — Write is BLOCKED until this passes)

작성 후 업로드 전 반드시 검증:

- [ ] 모든 문장 개별 읽기 완료
- [ ] 존재하지 않는 한국어 단어 없음 (Category A)
- [ ] 잘못된 단어 선택 없음 (Category B)
- [ ] 조사 오류 없음 은/는, 이/가, 을/를 (Category C)
- [ ] 번역체 패턴 없음 (Category D)
- [ ] 기술 외래어 표기 정확 (디펜던시, 렌더링, 컨텍스트 등)
- [ ] 한국 개발자가 쓴 것처럼 읽힘

**하나라도 실패 시 → 수정 후 재검증. 통과 전까지 파일 쓰기 금지.**

---

### STEP 6: Write Markdown Files

STEP 5 완료 후에만 실행.

```bash
# 출력 경로
~/devlog/{repo-name}/{YYYY-MM-DD}/

# 파일명 예시
트러블슈팅-MDC-roomCode-누락.md
기능구현-게임시작-로그.md
인사이트.md
다음과제.md
```

Write 툴로 각 파일 작성. 파일명의 공백은 `-`로 대체.

---

## Notes

- **본인 커밋만 처리**: `--author="<YOUR_NAME>"` 필터 필수. 다른 팀원 커밋 절대 포함 금지.
- 오늘 커밋 없으면 최근 커밋 + 현재 변경사항 + 세션 대화 기반으로 작성.
- 여러 소규모 커밋이 하나의 기능이면 하나의 파일로 통합.
- git commits = "무엇을 했는가"의 기준, session 대화 = "왜 했는가"의 기준.
