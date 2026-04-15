# find-skills

사용자가 "X를 어떻게 하나요?", "X 스킬 찾아줘", "X 기능이 있나요?" 라고 물으면 스킬 생태계에서 검색해 설치 가능한 스킬을 찾아주는 스킬.

> 출처: [skills.sh](https://skills.sh/) 생태계

## 기능

- 사용자의 요청에서 도메인과 태스크를 자동으로 파악
- [skills.sh 리더보드](https://skills.sh/)에서 인기 스킬 먼저 확인
- `npx skills find [query]`로 스킬 검색
- 품질 기준 확인 (설치 수, 소스 신뢰도, GitHub 스타)
- 스킬 발견 시 설치 명령어와 함께 옵션 제시
- 적합한 스킬이 없으면 직접 도움 제공 또는 새 스킬 생성 안내

## 주요 스킬 카테고리

| 카테고리 | 예시 검색어 |
|---------|------------|
| 웹 개발 | react, nextjs, typescript, css, tailwind |
| 테스팅 | testing, jest, playwright, e2e |
| DevOps | deploy, docker, kubernetes, ci-cd |
| 문서화 | docs, readme, changelog, api-docs |
| 코드 품질 | review, lint, refactor, best-practices |
| 디자인 | ui, ux, design-system, accessibility |
| 생산성 | workflow, automation, git |

## 설치

```bash
git clone https://github.com/treejh/ai-agent-skills.git ~/ai-agent-skills
ln -s ~/ai-agent-skills/skills/find-skills ~/.claude/skills/find-skills
```

## 사용법

이 스킬은 직접 호출보다 **자동 트리거** 방식으로 동작한다.

```
"React 성능 최적화 스킬 있나요?"
"PR 리뷰를 자동화할 수 있나요?"
"changelog 생성 스킬 찾아줘"
```

Claude가 해당 요청을 감지하면 자동으로 스킬을 검색하고 추천한다.

## 스킬 수동 검색

```bash
# 검색
npx skills find react performance

# 설치
npx skills add vercel-labs/agent-skills@react-best-practices -g -y
```

## 언제 사용하나

- 특정 도메인의 전문 스킬이 있는지 알고 싶을 때
- 반복 작업을 자동화하는 스킬을 찾고 싶을 때
- 에이전트 기능을 확장하고 싶을 때
