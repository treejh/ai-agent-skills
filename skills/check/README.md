# check

구현 완료 후 또는 머지 전에 코드 변경사항을 자동으로 검토하는 스킬. diff를 읽고 안전하게 수정 가능한 이슈를 자동으로 처리하며, 복잡한 diff에 대해서는 보안·아키텍처 전문 리뷰어를 병렬로 실행한다.

> 출처: [obra/superpowers](https://github.com/obra/superpowers) (v3.8.0)

## 기능

- diff 크기에 따른 리뷰 깊이 자동 분류 (Quick / Standard / Deep)
- 범위 이탈(scope drift) 감지 — 목표와 무관한 변경 플래그
- Hard Stop 항목 자동 검사 (주입 취약점, 미존재 식별자, 의존성 변경 등)
- 보안·아키텍처 전문 리뷰어 서브에이전트 병렬 실행 (Standard/Deep)
- 안전한 수정사항(`safe_auto`) 즉시 적용, 판단 필요 항목 사용자 확인 요청
- Deep 모드: 적대적 패스(adversarial pass) — "이 diff로 시스템을 어떻게 공격할 수 있나?"

### 리뷰 깊이 분류

| 깊이 | 기준 | 리뷰어 |
|------|------|--------|
| **Quick** | 100줄 미만, 1-5개 파일 | 기본 리뷰만 |
| **Standard** | 100-500줄 또는 6-10개 파일 | 기본 + 조건부 전문가 |
| **Deep** | 500줄+, 10개+ 파일, 또는 인증/결제/데이터 변경 | 기본 + 전체 전문가 + 적대적 패스 |

### 자동 수정 분류

| 클래스 | 정의 | 처리 |
|--------|------|------|
| `safe_auto` | 타이포, 누락 임포트, 스타일 | 즉시 적용 |
| `gated_auto` | null 체크, 에러 핸들링 추가 | 일괄 사용자 확인 |
| `manual` | 아키텍처, 동작 변경, 보안 트레이드오프 | 사인오프에서 제시 |
| `advisory` | 정보성 | 노트 기록 |

## 설치

```bash
git clone https://github.com/treejh/ai-agent-skills.git ~/ai-agent-skills
ln -s ~/ai-agent-skills/skills/check ~/.claude/skills/check
```

## 사용법

구현 작업이 끝난 후 Claude Code 세션에서:

```
/check
```

머지 전에도 동일하게 실행:

```
/check
```

## 출력 예시

```
files changed:    12 (+347 -89)
scope:            on target
review depth:     standard
hard stops:       2 found, 2 fixed, 0 deferred
specialists:      security, architecture
new tests:        3
verification:     npm test -> pass
```

## 언제 사용하나

- 기능 구현이 완료됐을 때
- PR 생성 전
- 코드 머지 전 최종 검토 시

> **사용 안 할 때**: 아이디어 탐색, 버그 디버깅 (디버깅은 `/hunt` 사용)
