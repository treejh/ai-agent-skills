# Plugins

Claude Code 플러그인 모음. 스킬과 달리 여러 스킬/에이전트/커맨드를 하나로 묶은 패키지.

> 출처: [team-attention/plugins-for-claude-natives](https://github.com/team-attention/plugins-for-claude-natives), [DrCatHicks/learning-opportunities](https://github.com/DrCatHicks/learning-opportunities)

---

## 스킬 vs 플러그인

| | 스킬 | 플러그인 |
|--|------|---------|
| 위치 | `~/.claude/skills/` | `~/.claude/plugins/` |
| 구성 | SKILL.md 단일 파일 | 스킬 + 에이전트 + 커맨드 묶음 |
| 설치 | 파일 복사/링크 | `/plugin install` 또는 직접 복사 |

**호출 방식은 동일** — SKILL.md의 `description`에 trigger 조건이 있어서 해당 상황이 되면 자동 활성화.

---

## 플러그인 목록

### 개발 워크플로우

| 플러그인 | 트리거 | 설명 |
|---------|--------|------|
| [clarify](clarify/) | "요구사항 명확히", "뭘 원하는 건지", `/clarify` | 모호한 요구사항을 구체적 스펙으로 정리 (3종 스킬) |
| [dev](dev/) | `/dev-scan` | 코드베이스 탐색 + 기술 결정 분석 멀티에이전트 |
| [session-wrap](session-wrap/) | "세션 마무리", "wrap up" | 세션 종료 시 5개 에이전트가 학습/자동화/문서 분석 |
| [interactive-review](interactive-review/) | `/review` | 마크다운 문서를 웹 UI로 인터랙티브 리뷰 |
| [codex](codex/) | `/codex:review`, `/codex:rescue`, `/codex:status` | OpenAI Codex CLI 연동 — 코드 리뷰, 디버깅, 리팩토링 위임 |
| [learning-opportunities](learning-opportunities/) | 아키텍처 작업 완료 후 자동 제안, `/learning-opportunities` | AI 코딩 중 실제 실력 향상을 위한 학습 과학 기반 인터랙티브 연습 |

### 학습 지원 (learning-opportunities 세부)

| 스킬 | 트리거 | 설명 |
|------|--------|------|
| `learning-opportunities` | 신규 파일·스키마 변경·리팩토링 후 자동 제안 | 예측→관찰→반성 등 6가지 인터랙티브 연습 제공 |
| `orient:orient` | `/orient:orient` (명시적 호출만) | 레포 구조 분석 후 `orientation.md` 생성 — `/learning-opportunities orient`의 재료 |

### 요구사항 분석 (clarify 세부)

| 스킬 | 트리거 | 설명 |
|------|--------|------|
| `clarify:vague` | "뭘 원하는 건지", "요구사항 정리" | 모호한 요청을 가설 기반 질문으로 구체화 |
| `clarify:unknown` | "뭘 모르는지 모르겠어", "전략 점검", "blind spots" | Known/Unknown 4분면으로 숨겨진 가정 발굴 |
| `clarify:metamedium` | "형식을 바꿔볼까", "다른 방법 없을까" | 내용(what) vs 형식(how) 관점 전환 |

---

## 설치

```bash
# 레포 클론 후 원하는 플러그인만 선택해서 설치
git clone https://github.com/treejh/ai-agent-skills.git ~/ai-agent-skills

cp -r ~/ai-agent-skills/plugins/clarify ~/.claude/plugins/clarify
cp -r ~/ai-agent-skills/plugins/session-wrap ~/.claude/plugins/session-wrap

# 또는 전체 설치
cp -r ~/ai-agent-skills/plugins/* ~/.claude/plugins/
```

---

## 추천 조합

### 개발 세션 기본 세트

```
clarify      ← 요구사항 불명확할 때 자동 개입
session-wrap ← 세션 마무리 시 자동 분석
```

### 아이디어 → 구현 플로우

```
clarify:vague     ← 아이디어를 구체적 스펙으로
  ↓
clarify:unknown   ← 놓친 가정/리스크 발굴
  ↓
dev               ← 코드베이스 탐색 + 기술 결정 분석
```

### 새 레포 온보딩 세트

```
/orient:orient                    ← 레포 분석 후 orientation.md 생성 (~30초)
  ↓
/learning-opportunities orient    ← 레포 구조 이해 학습 연습 (10-15분)
  ↓
개발 시작
```

### AI 코딩 실력 향상 세트

```
기능 구현
  ↓
/learning-opportunities           ← 방금 만든 코드로 능동 학습
  ↓
/session-wrap                     ← 세션 전체 정리 및 회고
```
