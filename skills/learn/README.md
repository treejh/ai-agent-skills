# learn

낯선 도메인을 깊이 탐구하거나, 리서치 아티클을 준비하거나, 수집한 자료를 출판 가능한 결과물로 만들 때 사용하는 스킬. 6단계 워크플로우(수집 → 소화 → 아웃라인 → 작성 → 다듬기 → 출판)를 실행한다.

> 출처: [obra/superpowers](https://github.com/obra/superpowers) (v3.8.0)

## 6단계 워크플로우

| 단계 | 이름 | 설명 |
|------|------|------|
| Phase 1 | **Collect** | 1차 소스(논문, 공식 블로그, 개발자 레포) 수집 |
| Phase 2 | **Digest** | 자료 검토 후 절반 이상 제거, 핵심만 남기기 |
| Phase 3 | **Outline** | 아웃라인 작성 (소스 없는 섹션은 삭제 또는 소스 추가) |
| Phase 4 | **Fill In** | 섹션별 작성, 막히면 Phase 2로 복귀 |
| Phase 5 | **Refine** | 중복 제거, 논리 흐름 점검, AI 패턴 제거 (`/write` 실행) |
| Phase 6 | **Publish** | 사용자가 처음부터 끝까지 직접 선형 읽기 후 출판 |

## 모드 선택

| 모드 | 목표 | 시작 | 종료 |
|------|------|------|------|
| **Deep Research** | 도메인을 충분히 이해해서 글 쓰기 | Phase 1 | Phase 6 |
| **Quick Reference** | 빠르게 정신 모델 구축, 글 계획 없음 | Phase 2 | Phase 2 (노트만) |
| **Write to Learn** | 이미 자료 있음, 글쓰기로 이해 심화 | Phase 3 | Phase 6 |

## Hard Rules

- Phase 5 건너뛰기 금지 — 항상 다듬기 후 출판
- Phase 1은 1차 소스만 — 요약본, 해설글은 소스가 아님
- 소스 간 모순은 반드시 둘 다 기록 — 조용히 하나 선택 금지
- Phase 6 최종 자기검토는 사용자가 직접 — AI 대신 불가

## 설치

```bash
git clone https://github.com/treejh/ai-agent-skills.git ~/ai-agent-skills
ln -s ~/ai-agent-skills/skills/learn ~/.claude/skills/learn
```

> `learn`은 내부적으로 `/write` 스킬을 사용한다. 함께 설치 권장:
> ```bash
> ln -s ~/ai-agent-skills/skills/write ~/.claude/skills/write
> ```

## 사용법

```
/learn
```

주제나 목표를 설명하면 모드를 선택하고 워크플로우를 시작한다.

## 언제 사용하나

- 새로운 기술 스택이나 아키텍처 패턴을 깊이 이해하고 싶을 때
- 테크 블로그 포스트나 내부 문서를 쓰기 위해 리서치할 때
- 이미 수집한 자료를 정리해서 출판 가능한 글로 만들 때

> **사용 안 할 때**: 빠른 검색, 단일 파일 읽기
