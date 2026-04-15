# hunt

에러, 크래시, 예상치 못한 동작, 실패하는 테스트를 디버깅할 때 사용하는 스킬. 수정 전에 반드시 근본 원인을 찾는다.

> 출처: [obra/superpowers](https://github.com/obra/superpowers) (v3.8.0)

## 핵심 원칙

**수정 전에 근본 원인을 한 문장으로 말할 수 있어야 한다:**
> "나는 [X]가 근본 원인이라고 생각한다. 왜냐하면 [증거]이기 때문이다."

구체적인 파일, 함수, 라인, 조건을 명시해야 한다. "상태 관리 문제"는 테스트할 수 없다. "`src/hooks/user.ts:42`의 `useUser`에서 의존성 배열에 `userId`가 빠져서 오래된 캐시가 남는 것"은 테스트할 수 있다.

## 기능

- 가설 없는 무작위 수정 방지
- 증거 기반 디버깅 강제 (로그 라인, 실패 어서션, 최소 테스트)
- 3번 실패 후 사용자에게 상황 보고 및 방향 확인
- 합리화 패턴 감지 ("그냥 이거 한번 해보면", "아마 X일 거야", "전에도 이런 문제였는데")
- 진행 신호 추적 — 가설이 올바른 방향으로 가고 있는지 확인

## Hard Rules

- 같은 증상이 수정 후에도 나타나면 즉시 멈추고 처음부터 재분석
- 3번 가설 실패 시 사용자에게 현황 보고
- 환경 정보를 기억에서 말하지 말고 반드시 명령어로 확인 (`sw_vers`, `node --version` 등)
- Grep 없이 코드 수정 금지 — 존재 여부를 먼저 확인

## 설치

```bash
git clone https://github.com/treejh/ai-agent-skills.git ~/ai-agent-skills
ln -s ~/ai-agent-skills/skills/hunt ~/.claude/skills/hunt
```

## 사용법

```
/hunt
```

## 출력 예시

```
Root cause:  useUser 훅의 deps 배열에 userId 누락 (src/hooks/user.ts:42)
Fix:         deps에 userId 추가
Confirmed:   userId 변경 시 이전에는 리렌더링 없음 → 수정 후 정상 리렌더링
Tests:       12 pass, 0 fail / regression: src/hooks/__tests__/user.test.ts
Status:      resolved
```

## 언제 사용하나

- 에러나 크래시 발생 시
- 테스트가 예상치 않게 실패할 때
- 코드가 이상하게 동작할 때

> **사용 안 할 때**: 코드 리뷰, 새 기능 개발 (새 기능 설계는 `/think` 사용)
