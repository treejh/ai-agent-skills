# ai-agent-skills

Claude Code와 AI 코딩 에이전트를 위한 **개발 워크플로우 스킬 & 커맨드 모음**.

매일 반복되는 개발 작업(커밋, PR, 데브로그, TDD 등)을 자동화하거나 품질을 높이는 데 초점.

---

## 스킬 목록

### 내가 만든 스킬

| 스킬 | 설명 | 문서 |
|------|------|------|
| [cnotion](skills/cnotion/) | 오늘 작업을 분석해서 Notion DB에 엔지니어링 블로그 스타일로 업로드 | [README](skills/cnotion/README.md) |
| [cobsidian](skills/cobsidian/) | 오늘 작업을 분석해서 로컬 `~/devlog/`에 마크다운으로 저장 (Obsidian 친화적) | [README](skills/cobsidian/README.md) |
| [korean-narration](skills/korean-narration/) | 툴 실행 시 무슨 작업인지 한국어로 한 줄 설명 추가 | [README](skills/korean-narration/README.md) |

### 외부에서 가져온 스킬

| 스킬 | 설명 | 출처 | 문서 |
|------|------|------|------|
| [writing-plans](skills/writing-plans/) | 코드 작성 전 단계별 구현 계획 문서 생성 | [obra/superpowers](https://github.com/obra/superpowers) | [README](skills/writing-plans/README.md) |
| [test-driven-development](skills/test-driven-development/) | Red→Green→Refactor TDD 워크플로우 강제 | [obra/superpowers](https://github.com/obra/superpowers) | [README](skills/test-driven-development/README.md) |
| [subagent-driven-development](skills/subagent-driven-development/) | 독립 태스크를 여러 서브에이전트에게 병렬 위임 | [obra/superpowers](https://github.com/obra/superpowers) | [README](skills/subagent-driven-development/README.md) |
| [claude-orchestrator](skills/claude-orchestrator/) | Claude + Codex + Gemini 오케스트레이션 설정 | [gaebalai/claude-code-orchestrator](https://github.com/gaebalai/claude-code-orchestrator) | [README](skills/claude-orchestrator/README.md) |

---

## 커맨드 목록

| 커맨드 | 호출 | 설명 |
|--------|------|------|
| [ccm](commands/ccm.md) | `/ccm` | 변경 파일을 기능 단위로 그룹화해서 Conventional Commit 생성 |
| [cpr](commands/cpr.md) | `/cpr` | 브랜치 커밋 분석해서 한국어 PR 설명 작성 및 PR 생성 |

자세한 내용은 [commands/README.md](commands/README.md) 참고.

---

## 플러그인 목록

스킬 여러 개를 묶은 패키지. 설치하면 트리거 조건에 맞을 때 자동 활성화.

| 플러그인 | 트리거 | 설명 |
|---------|--------|------|
| [clarify](plugins/clarify/) | "요구사항 명확히", "뭘 원하는 건지" | 모호한 요구사항 → 구체적 스펙 (vague/unknown/metamedium 3종) |
| [dev](plugins/dev/) | `/dev-scan` | 코드베이스 탐색 + 기술 결정 분석 멀티에이전트 |
| [session-wrap](plugins/session-wrap/) | "wrap up", "세션 마무리" | 5개 에이전트가 학습/자동화/문서/태스크 분석 |
| [interactive-review](plugins/interactive-review/) | `/review` | 마크다운을 웹 UI로 인터랙티브 리뷰 |
| [agent-council](plugins/agent-council/) | `/agent-council` | Claude + Codex + Gemini 동시 질의 후 의견 종합 |
| [team-assemble](plugins/team-assemble/) | `/team-assemble` | 태스크에 맞는 에이전트 팀 동적 구성 |

자세한 내용은 [plugins/README.md](plugins/README.md) 참고.

---

## 설치

### 1. 레포 클론

```bash
git clone https://github.com/treejh/ai-agent-skills.git ~/ai-agent-skills
```

### 2. 스킬 연결

사용할 스킬을 Claude Code의 skills 디렉토리에 심볼릭 링크로 연결:

```bash
# 예시: cobsidian 스킬 설치
ln -s ~/ai-agent-skills/skills/cobsidian ~/.claude/skills/cobsidian

# 예시: cnotion 스킬 설치
ln -s ~/ai-agent-skills/skills/cnotion ~/.claude/skills/cnotion

# 예시: writing-plans 설치 (Codex에도 사용 가능)
ln -s ~/ai-agent-skills/skills/writing-plans ~/.claude/skills/writing-plans
ln -s ~/ai-agent-skills/skills/writing-plans ~/.codex/skills/writing-plans
```

### 3. 커맨드 연결

```bash
ln -s ~/ai-agent-skills/commands/ccm.md ~/.claude/commands/ccm.md
ln -s ~/ai-agent-skills/commands/cpr.md ~/.claude/commands/cpr.md
```

### 4. 플러그인 설치

```bash
# 원하는 플러그인만 선택
cp -r ~/ai-agent-skills/plugins/clarify ~/.claude/plugins/clarify
cp -r ~/ai-agent-skills/plugins/session-wrap ~/.claude/plugins/session-wrap
cp -r ~/ai-agent-skills/plugins/doubt ~/.claude/plugins/doubt

# 또는 전체 설치
cp -r ~/ai-agent-skills/plugins/* ~/.claude/plugins/
```

---

## 추천 워크플로우

### 일일 개발 → 기록

```
개발 작업
  ↓
/ccm          ← 기능 단위로 Conventional Commit 생성
  ↓
/cpr          ← PR 설명 자동 작성 및 생성
  ↓
/cobsidian    ← 오늘 작업 로컬 데브로그에 저장
또는
/cnotion      ← 오늘 작업 Notion에 업로드
```

### 새 기능 개발

```
/writing-plans              ← 구현 계획 먼저 수립
  ↓
/test-driven-development    ← TDD로 구현
  ↓
/subagent-driven-development  ← 독립 태스크 많으면 병렬 처리
  ↓
/ccm                        ← 커밋
```

---

## 스킬 설치 경로

| 에이전트 | skills 경로 | commands 경로 |
|---------|------------|--------------|
| Claude Code | `~/.claude/skills/` | `~/.claude/commands/` |
| Codex | `~/.codex/skills/` | — |

---

## 기여

이 레포는 개인 사용 목적으로 정리한 것이지만, 개선 아이디어나 버그는 Issue로 남겨주세요.
