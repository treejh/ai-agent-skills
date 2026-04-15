# health

Claude가 지시를 무시하거나 일관성 없이 동작하거나, 훅이 오작동하거나, MCP 서버를 감사해야 할 때 사용하는 스킬. Claude Code의 6계층 설정 스택 전체를 감사하고 심각도별로 이슈를 표시한다.

> 출처: [obra/superpowers](https://github.com/obra/superpowers) (v3.8.0)

## 6계층 감사 프레임워크

```
CLAUDE.md → rules → skills → hooks → subagents → verifiers
```

## 기능

- 프로젝트 규모별 티어 자동 분류 (Simple / Standard / Complex)
- 설정 데이터 자동 수집 (스크립트 실행)
- MCP 서버 라이브 상태 확인
- 2개의 전문 분석 에이전트 병렬 실행:
  - Agent 1: 컨텍스트 + 보안 감사
  - Agent 2: 제어 + 동작 감사
- 이슈를 3단계로 분류하여 보고:
  - `[!]` Critical — 즉시 수정
  - `[~]` Structural — 곧 수정
  - `[-]` Incremental — 나중에 개선

### 프로젝트 티어

| 티어 | 신호 | 예상 설정 |
|------|------|----------|
| **Simple** | 500개 미만 파일, 1인 개발, CI 없음 | CLAUDE.md만; 스킬 0-1개 |
| **Standard** | 500-5K 파일, 소규모 팀 또는 CI | CLAUDE.md + rules; 스킬 2-4개 |
| **Complex** | 5K+ 파일, 다중 기여자, CI 활성 | 전체 6계층 설정 필요 |

## 설치

```bash
git clone https://github.com/treejh/ai-agent-skills.git ~/ai-agent-skills
ln -s ~/ai-agent-skills/skills/health ~/.claude/skills/health
```

## 사용법

```
/health
```

## 출력 예시

```
Health Report: my-project (Standard tier, 1,234 files)

### [PASS] Passing
| Check                    | Detail |
|--------------------------|--------|
| settings.local.json gitignored | ok |
| No nested CLAUDE.md      | ok |
| Skill security scan      | no flags |

### [!] Critical — fix now
- hooks.on_stop 누락: 세션 종료 후 요약이 실행되지 않음

### [~] Structural — fix soon
- SKILL.md description이 50자를 초과함 (컨텍스트 낭비)

### [-] Incremental — nice to have
- global CLAUDE.md에 project-level 규칙이 섞여 있음
```

## 언제 사용하나

- Claude가 지시를 무시하거나 이상하게 동작할 때
- 훅이 의도대로 실행되지 않을 때
- MCP 서버 연결 상태를 점검하고 싶을 때
- 설정 스택 전반을 정기적으로 감사할 때

> **사용 안 할 때**: 코드 디버깅, PR 리뷰
