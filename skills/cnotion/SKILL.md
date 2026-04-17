---
name: cnotion
description: Analyze today's work (git commits + session conversation) and upload categorized pages to a Notion DB. Each page is written in engineering blog style with why/trade-off/before-after structure. Use /cnotion analyze for session insight extraction mode.
version: 5.0.0
---

# cnotion — Daily Work → Notion DB Upload

## Purpose

Analyze today's work and upload categorized pages to a Notion DB.
Write in **engineering blog style** — natural prose paragraphs, not bullet-point lists.
All output content is written in **Korean**. This skill document is in English for instruction-following accuracy.

---

## Notion DB Info (Configure for your workspace)

> **Setup required**: Replace the values below with your own Notion DB information.
> See [README.md](./README.md) for setup instructions.

- **DB page**: `https://www.notion.so/<YOUR_DB_PAGE_ID>`
- **DB parent page ID**: `<YOUR_PARENT_PAGE_ID>`
- **Data Source ID**: `collection://<YOUR_COLLECTION_ID>`

### DB Schema

| Field | Type | Description |
|-------|------|-------------|
| 내용 | title | Page title (work topic) |
| 제목 | text | Subtitle or one-line summary |
| 날짜 | date | Work date (today) |
| 태그 | multi_select | Category: 기능 구현 / 트러블슈팅 / 인프라 / 리팩토링 / 인사이트 / 다음 과제 |

---

## Execution Steps

### STEP 1: Collect Today's Work

#### 1-1. Gather today's git commits
```bash
git log --oneline --since="YYYY-MM-DD 00:00:00" --all

# If none, get recent commits
git log --format="%H %ad %s" --date=format:"%Y-%m-%d %H:%M" -15
```

#### 1-2. Inspect each commit
```bash
git show --stat <hash>
git show <hash>
```
Read key files if needed.

#### 1-3. Check uncommitted changes
```bash
git status
git diff --stat
```

#### 1-4. Analyze session conversation (Critical)

Extract from the current Codex session:
- Concepts learned during implementation ("so that's how this pattern works")
- Design deliberations ("A vs B — why we chose B")
- Misconceptions corrected ("thought it was A, turned out to be B")
- Interesting patterns or structures discovered in code
- Understanding gained from error messages

**These do NOT appear in commits but are the core material for Insight and Troubleshooting pages.**

---

### STEP 2: Derive Category-Based Work Items

Group multiple commits into one item if they belong to the same topic.

**Each item = 1 Notion DB page (row)**

| Tag Value | Criteria |
|-----------|----------|
| 기능 구현 | New feature / API / logic |
| 트러블슈팅 | Bug found → root cause → fix |
| 인프라 | CI/CD, Docker, GitHub Actions, AWS |
| 리팩토링 | Code structure improvement |
| 인사이트 | Lessons learned (ALWAYS create) |
| 다음 과제 | Unresolved issues, next steps (ALWAYS create) |

---

### STEP 3: Draft Page Body — Engineering Document Quality

**⚠️ This step produces DRAFTS only. Do NOT upload yet. STEP 5 validation is mandatory before upload.**

**Core principle: No bare descriptions. Every page MUST include "Why did we do this → What did we choose → Why that choice → How did the outcome change."**

Every page focuses on real operational problems and measurable improvements.
Statements like "implemented this feature" without context are prohibited.

---

#### 🔥 Troubleshooting Page

Structure: **Problem Definition → Root Cause → Design Decision → Before/After**

```
[intro paragraph]
What symptom appeared in production. What was initially suspected.
Lead into how the actual cause turned out to be different.

[heading_1] 문제 정의
[paragraph] What situation produced what symptom.
Not just "error occurred" — state the operational impact
(game unplayable, log gap, memory leak, etc.).
Example: "During peak time with 300 concurrent users, MDC roomCode was empty,
making it impossible to trace which room had issues from logs alone."

[heading_1] 근본 원인 분석
[paragraph] Explain the root cause, not surface symptoms.
Include how the relevant technology layer (Spring/JVM/WebSocket) works
and why this architecture inevitably produces this problem.
[code block] Problematic code or config (Before)

[heading_1] 설계 결정 및 트레이드오프
[paragraph] If multiple solutions existed, explain why this one was chosen.
State rejected alternatives and why. Example:
- Option A (TaskDecorator): method didn't exist due to Spring version incompatibility
- Option B (ExecutorChannelInterceptor): guarantees execution at executor thread entry → adopted
State trade-offs (complexity increase, performance impact, maintainability).

[heading_1] 해결 과정
[paragraph] What was changed and how.
[code block] Before code
[code block] After code

[heading_1] 개선 효과 (Before / After)
[paragraph] Concrete change in metrics or operational behavior.
Example: "Previously, [room:] [user:] fields were always empty in game handler logs,
requiring manual session ID cross-referencing. Now, a single
grep '[room:361181]' game-event.log reconstructs the entire event flow."

[heading_1] 재발 방지 / 주의사항
[paragraph] What to suspect first in similar situations.
Side effects introduced by this change.
```

---

#### 🚀 Feature Implementation Page

Structure: **Problem Definition → Design Decision → Trade-off → Implementation → Operational Effect**

```
[intro paragraph]
What concrete problem existed when this feature was missing.
Start with "this was actually broken/missing" not "would be nice to have."

[heading_1] 문제 정의
[paragraph] Describe the operational situation without this feature.
Example: "After a game ended, when a bug report came in, we couldn't even tell
when the game started from logs. Move logs existed but with no reference timestamp,
full timeline reconstruction was impossible."

[heading_1] 설계 결정
[paragraph] Where to add what, and why that location.
Example: "Game start is the moment RoomStatus changes to PLAYING.
Logging right after changeRoomStatus() in PlayFacade is the most accurate point."

[heading_1] 트레이드오프
[paragraph] Compare with other implementation locations/methods.
Example: "Could log in Handler layer, but game start logic concentrates in Facade.
Handler handles message routing — business event logs belong in Facade."

[heading_1] 구현 내용
[paragraph] What was added/changed.
[code block] Key code

[heading_1] 운영 효과 (Before / After)
[paragraph] What changed operationally after this feature.
Not "improved" — state "this is now possible" with specifics.
```

---

#### 🏗️ Infrastructure/Deployment Page

```
[intro paragraph]
What operational pain or risk existed. Why this change was needed now.

[heading_1] 문제 정의
[paragraph] What part of existing infra/config was problematic, from ops perspective.
Example: "No log config for staging — after deployment, no log files were created,
nothing in CloudWatch, only way was SSH into EC2 directly."

[heading_1] 설계 결정 및 트레이드오프
[paragraph] How it was designed, compared with alternatives.
[code block] Changed config (Before → After)

[heading_1] 운영 효과
[paragraph] Concrete change in operational experience after the change.
```

---

#### 💡 Insight Page (ALWAYS create)

Document technical insights gained from today's work, from a practical perspective.

```
[intro paragraph]
1-2 sentence summary: what problem led to what new understanding.

[heading_1] 새롭게 이해한 기술 개념
[paragraph] Each concept as an independent paragraph.
Order: "Why does this concept exist → How does it work → When does it become a problem."
Use heading_2 or **bold concept name** to separate.
Not just definitions — connect to today's actual situation.

[heading_1] 설계 판단력이 생긴 것
[paragraph] How to decide next time a similar choice appears.
Format: "In situation A, choose B, because C."

[heading_1] 삽질에서 배운 것
[paragraph] What was misunderstood initially and corrected.
Story format: "Thought X, tried Y, but actually Z — failed because..."

[heading_1] 다음 번엔 이렇게 접근하자
[paragraph] What to check first in similar situations, based on this experience.
```

---

#### 🤔 Next Tasks Page (ALWAYS create)

```
[intro paragraph]
Background: what was left unresolved or feels incomplete from today's work.

[heading_1] 아직 해결 안 된 문제
[paragraph] Unresolved issues with context on why they weren't resolved today.
Not a TODO list — include "what operational risk exists if this stays unresolved."

[heading_1] 동작은 하지만 개선이 필요한 부분
[paragraph] Current implementation limits or better directions.
Include why it wasn't changed right now.

[heading_1] 다음 작업에서 검증해야 할 것
[paragraph] Verification points for whether today's changes work as expected in production.
Example: "After dev deployment, verify MDC roomCode actually appears in game handler logs."
```

---

## Writing Rules

### Content Quality Rules

- Write in natural Korean. Technical accuracy is the top priority.
- No translationese (번역체) — write like a Korean backend developer actually speaks.
- No abstract descriptions — every sentence must connect to a specific situation, metric, or code.
- Never end with "improved" or "resolved" without stating WHAT changed and HOW.
- Style target: internal team technical archive, not a casual blog. Design decisions and trade-offs are mandatory.
- Technical terms are allowed but must be followed by operational context in the next sentence.
- Before uploading, test each sentence: "Can a teammate unfamiliar with this project understand this?"

### Quantify Improvement Effects

Always include metrics when possible:
- Response time: 200ms → 80ms
- Log analysis time: 10 min manual search → single grep in 10 sec
- Duplicate logs: 1,800/min × 2 → 1,800/min

When metrics are unavailable, express as operational behavior change:
- "SSH into EC2 + manual grep → instant query in CloudWatch"

### Design Decisions Must State Judgment Criteria

Not just "chose this method" — state what criteria drove the choice.

Criteria examples:
- 정확성 > 구현 난이도
- 성능 > 가독성
- 단순성 > 확장성
- 운영 안정성 > 개발 편의성

Example:
> Chose ExecutorChannelInterceptor over TaskDecorator.
> Criteria: Accuracy > Implementation difficulty.
> TaskDecorator method didn't exist due to Spring version incompatibility.
> ExecutorChannelInterceptor.beforeHandle() guarantees executor thread entry timing,
> enabling precise MDC setup timing control.

### Output Quality Rule (Critical)

Every section must end with a concrete outcome.

❌ Bad:
"문제가 해결되었다"
"성능이 개선되었다"

✅ Good:
"이 변경으로 인해 로그 추적 시간이 10분 → 10초로 단축되었다"
"이제 특정 roomCode 기준으로 전체 이벤트 흐름을 한 번에 조회할 수 있다"

If the outcome is not measurable, describe the operational behavior change.

### Depth Rule (Critical)

Do NOT stop at surface-level explanations.

Every design decision MUST include:

- Why alternative approaches were rejected
- What would break if this decision was wrong
- Under what conditions this decision should be reconsidered

If these are missing, the document is incomplete.

### Operational Perspective Rule (Critical)

Every section must explicitly answer:

"Why does this matter in production?"

If a sentence does not connect to real system behavior,
user impact, or operational workflow, rewrite it.

### Information Scarcity Rule (Fail-safe)

When commit info is insufficient, code changes are unclear, or session evidence is lacking:

- Do NOT speculate.
- Do NOT exaggerate.
- Write only based on confirmed facts.

When needed, state explicitly:
> "현재 정보만으로는 정확한 원인을 단정할 수 없지만, 가능성 있는 원인은 다음과 같다."

or:
> "코드 변경 내용이 명확하지 않아 구현 상세는 생략한다."

### Deduplication and Document Quality

- Merge same-topic work into a single page.
- If content overlaps with existing pages, focus only on key differences.
- Cut unnecessary length — center on key decisions.

Target audience:
- A backend developer seeing this project for the first time
- A teammate with no domain knowledge

Therefore: every page must be self-contained and understandable without external context.
Use technical terms but explain the operational context in the next sentence.

### Code Rules

- Must be actually runnable code
- Strip unnecessary code — show only the essential parts
- Before/After comparison must be clear
- Comments explain "why", not "what"

### Document Level Options

Selectable on request:

- `level: blog` → readable, explanation-focused
- `level: engineering` → design/trade-off-focused (default)
- `level: portfolio` → achievement/impact-focused

---

## STEP 4: Korean Encoding Rule (Critical)

All Korean text in Notion page content MUST use literal Korean characters.
NEVER use Unicode escape sequences.

❌ Wrong: `\uac8c\uc784 \uc885\ub8cc`, `\ud14c\uc2a4\ud2b8`
✅ Correct: `게임 종료`, `테스트`

This applies to ALL content fields: paragraphs, table cells, code blocks, headings.
`\uXXXX` sequences render as literal escape text on Notion pages.

---

## STEP 5: Korean Language Validation (MANDATORY GATE — Upload is BLOCKED until this passes)

### ⛔ HARD RULE: This step is a GATE. Do NOT call notion-create-pages until every sub-step below is complete.

The workflow is: **STEP 3 (draft) → STEP 5 (validate & fix) → STEP 6 (upload)**. There is no shortcut.

### Why this step exists

LLMs generate Korean text token-by-token and frequently produce:
- Non-existent Korean words that look plausible at a glance
- Wrong syllable assembly (e.g., "난지" instead of "남지")
- Particle errors invisible to English-trained attention
- Unnatural phrasing that no native speaker would write

These errors make the final Notion page look unprofessional and untrustworthy.
**Every single paragraph must pass validation before upload.**

---

### 5-1. Sentence-by-Sentence Re-read Protocol

For EACH paragraph in the draft:

1. **Read every sentence individually** — not skimming, not batch-checking.
2. For each sentence, ask: **"Would a native Korean backend developer actually write exactly this sentence in a team Slack or internal document?"**
3. If ANY word looks slightly unusual or unfamiliar → stop and verify it is a real Korean word.
4. If the sentence sounds even slightly unnatural → rewrite it while preserving meaning.

**Do NOT just re-read the draft once and move on.** Process each sentence as an independent unit.

---

### 5-2. Common LLM Korean Error Patterns (MUST check for ALL of these)

#### Category A: Non-existent words (hallucinated syllable combinations)

The model often generates words that do not exist in Korean. These look similar to real words but have wrong syllables.

| ❌ LLM Output | ✅ Correct | Error Type |
|---|---|---|
| 난지 | 남지 | Wrong final consonant (ㄴ→ㅁ) |
| 프로덤 | 프로덕션 | Truncated/garbled loanword |
| 탈리다 | 빠지다 / 누락되다 | Non-existent verb |
| 듐안 | 내부 | Garbled syllable |
| 쳄널 | 채널 | Wrong vowel in loanword |
| 점속적으로 | 지속적으로 | Similar-looking but different word |
| 랜더링 | 렌더링 | Wrong vowel (ㅐ→ㅔ) |
| 컨택스트 | 컨텍스트 | Wrong vowel in loanword |
| 디팬던시 | 디펜던시 | Wrong vowel |
| 윤토리 | 윷놀이 | Wrong syllable assembly |
| 퇁퇁 | 퇴장 | Garbled consonant cluster |
| 뱴돌 | 말(돌) | Non-existent syllable |
| 지목이나 다름없었다 | 지옥이나 다름없었다 | Wrong final consonant (ㅁ→ㄱ) |
| 매니져 | 매니저 | Wrong final syllable |

**Detection rule**: If a Korean word is not commonly used in tech blogs or documentation, it is probably wrong. Replace it with a standard term.

#### Category B: Wrong word choice (real word, wrong meaning)

| ❌ LLM Output | ✅ Correct | Why it's wrong |
|---|---|---|
| 먹어보는 날이었다 | 체감한 날이었다 | "먹어보다" = taste food, not "experience" |
| 매력 100% | 확신 100% | "매력" = charm, not confidence |
| 설계를 잘랐는데 | 설계를 잡았는데 | "자르다" = cut, not "establish" |
| 비어 눈다 | 비어 있다 | Non-existent verb ending |
| 로그를 때렸다 | 로그를 남겼다 | "때리다" = hit, not "write/leave" |
| 서버를 올렸다 | 서버를 기동했다 / 띄웠다 | Acceptable colloquially but check context |
| 에러를 잡았다 | 에러를 해결했다 / 원인을 찾았다 | "잡다" is too casual for engineering docs |
| 코드를 먹었다 | 코드가 적용되었다 | Slang, not appropriate for documentation |

**Detection rule**: Read the sentence literally. If the literal meaning doesn't match the intended meaning, the word choice is wrong.

#### Category C: Particle and grammar errors

| ❌ LLM Output | ✅ Correct | Rule |
|---|---|---|
| 서버는 시작한다 | 서버가 시작된다 | Subject marker 는→가 for new info |
| 로그을 남긴다 | 로그를 남긴다 | 을→를 after vowel-ending noun |
| 이 문제은 | 이 문제는 | 은→는 after vowel-ending noun |
| 설정이 필요한 것이다 | 설정이 필요하다 | Unnecessary nominalization |
| ~것이 가능하다 | ~할 수 있다 | Translationese from English "it is possible to" |
| ~에 대해서 처리한다 | ~을 처리한다 | Unnecessary "에 대해서" |
| ~하는 것을 수행한다 | ~한다 | Double nominalization |

#### Category D: Unnatural phrasing (translationese / 번역체)

| ❌ Translationese | ✅ Natural Korean |
|---|---|
| 이것은 ~한 문제를 가지고 있다 | ~한 문제가 있다 |
| ~를 수행하는 것이 필요하다 | ~해야 한다 |
| ~하는 것에 의해 | ~해서 / ~하면 |
| 그것은 ~때문이다 | ~때문이다 |
| 이 접근법은 ~를 제공한다 | 이 방식으로 ~할 수 있다 |
| ~의 경우에는 | ~면 / ~할 때 |
| ~를 가지고 있는 상태에서 | ~가 있는 상태에서 / ~인 채로 |
| ~하는 것이 관찰되었다 | ~한 것을 확인했다 |

**Detection rule**: If translating the sentence back to English produces more natural English than the Korean reads as Korean, it's translationese. Rewrite.

#### Category E: Sentence ending unnaturalness

| ❌ Unnatural | ✅ Natural |
|---|---|
| ~인 것이다 (overuse) | ~이다 / ~다 |
| ~되어지다 (passive + passive) | ~되다 |
| ~할 수 있게 되어진다 | ~할 수 있다 |
| ~라고 하는 것 | ~라는 것 |
| ~해 나가야 할 것이다 | ~해야 한다 |

---

### 5-3. Technical Term Verification

- Verify all loanword spellings against standard Korean tech usage:
  - 디펜던시 (not 디팬던시)
  - 렌더링 (not 랜더링)
  - 컨텍스트 (not 컨택스트)
  - 인터셉터 (not 인터쳅터)
  - 시리얼라이제이션 (not 시리얼라이져이션)
  - 리포지토리 (not 레포지터리) — though "레포" colloquially is OK
- If unsure about a loanword spelling, use the English original instead.

---

### 5-4. Full Draft Review Pass

After fixing individual sentences, re-read the ENTIRE page once more for:

1. **Flow**: Do paragraphs connect logically? No abrupt jumps?
2. **Consistency**: Same term used throughout? (Don't mix 인터셉터/인터셉트/Interceptor randomly)
3. **Redundancy**: Same point repeated in different words? Cut it.
4. **Tone**: Consistent engineering document tone throughout? No sudden shifts to casual blog tone?

---

### 5-5. Final Gate Check

Before proceeding to STEP 6, confirm ALL of the following:

- [ ] Every sentence has been individually read and verified
- [ ] No words from the Category A list (or similar patterns) remain
- [ ] No wrong word choices from Category B remain
- [ ] Particle usage (은/는, 이/가, 을/를) is correct throughout
- [ ] No translationese patterns from Category D remain
- [ ] Technical loanwords are spelled correctly
- [ ] The page reads like it was written by a Korean developer, not translated from English

**If ANY checkbox fails → fix it before upload. Do NOT proceed.**

---

## STEP 6: Upload via notion-create-pages

**Only after STEP 5 is fully complete.**

Call notion-create-pages for each item.

**Common settings:**
- `parent_id`: `df3faae9-6123-835a-8d98-819482189c2b`
- `날짜`: today's date (YYYY-MM-DD)
- `태그`: the matching category value

---

## Self-Review Checklist (Mandatory — before upload)

After writing, verify ALL of the following:

- [ ] Problem definition connects to a real operational situation?
- [ ] Design decision explains "why this was the best choice" sufficiently?
- [ ] Trade-offs are used as decision rationale, not just listed?
- [ ] Before/After is in a genuinely comparable format?
- [ ] A reader can understand without follow-up questions?
- [ ] **Korean language validation (STEP 5) is 100% complete?**
- [ ] **No hallucinated Korean words remain?**
- [ ] **No translationese patterns remain?**

Fix any deficiencies before uploading.

---

## Core Principles (Critical)

### ❌ Do NOT
- "Implemented this feature" → meaningless without why and what problem existed before
- "Improved" → meaningless without stating what changed and how
- Bullet-point feature lists
- Pure implementation description (what) — MUST include why and trade-off
- Upload without completing STEP 5 Korean validation

### ✅ MUST DO
- Problem definition: what operational problem existed before
- Design decision: why this method was chosen (with alternative comparison)
- Before/After: concrete change in code or operational experience
- Prevention / Caveats: what to check first in similar situations
- Insight and Next Tasks pages are ALWAYS created
- STEP 5 Korean validation is ALWAYS completed before upload

## Notes

- **Only include the user's own commits.** Use `git log --author` to filter by the user's name or email. NEVER create pages for other team members' work. If unsure of the author name, check with `git log --format="%an %ae" -5` first.
- If no commits today, use recent commits + current changes + session conversation
- Use currentDate for dates
- Group multiple small commits into one page if they form a single feature

## Grouping Work Items

When grouping work items:

1. Use git commits as the factual anchor to determine what was actually changed today.
2. Use session conversation as the primary source for why, trade-offs, and insights.

If there is a mismatch:
- Prefer git commits for "what happened"
- Prefer session for "why it happened"
- Do not infer or exaggerate beyond available evidence

---

# MODE 2: Session Insight Extraction

Triggered by `/cnotion analyze`.

Analyzes session conversation and uploads **structured learning insights** as a single Notion page.

On start, always output:

> "I'm using the cnotion skill to extract insights from this session."

---

## Insight Categories

### 1. New Concepts Learned
User learned a concept they didn't know before.
Triggers: What is / What's the difference / How does X work

### 2. Problems Solved
Encountered an error and identified root cause.
Triggers: error / 500 / exception / not working / why does this fail

### 3. Architecture / Design Decisions
Discussed system design or implementation direction.
Triggers: architecture / design / pattern / structure / how should I implement

### 4. Debugging Discoveries
Identified root cause of system behavior.
Examples: race conditions / websocket ordering / cache inconsistency / transaction boundaries

---

## Insight Filtering

Include: new technical knowledge, root cause discoveries, architecture decisions, important debugging finds.
Exclude: small talk, simple confirmations, trivial formatting questions.

## Insight Scoring (include only score ≥ 2)

| Type | Score |
|------|-------|
| Root cause discovery | +3 |
| Architecture decision | +3 |
| New technical concept | +2 |
| Simple clarification | +1 |

## Auto Tags

| Technology | Tag |
|-----------|-----|
| Spring Boot | #spring |
| Redis | #redis |
| WebSocket | #websocket |
| JPA | #jpa |
| Security | #security |
| Log4j2 | #logging |
| Architecture | #architecture |
| Debugging | #debugging |
| AWS | #aws |

---

## Notion Upload Format

Tag: `인사이트`
Title: `Session Insights – YYYY-MM-DD`

Body structure:

```
[heading_1] 🧠 New Concepts Learned
[paragraph] Concept name + explanation

[heading_1] 🔧 Problems Solved
[paragraph] Problem name + root cause

[heading_1] 🏗 Architecture Decisions
[paragraph] Decision + reasoning

[heading_1] 🐞 Debugging Discoveries
[paragraph] Discovery + significance

[heading_1] 💡 Key Takeaways
[paragraph] Core summary (3-5 lines)

[heading_1] 🏷 Tags
[paragraph] #spring #websocket #logging ...
```

---

## Insight Writing Rules

- Do NOT copy session transcript verbatim.
- Extract only high-value insights.
- Rewrite explanations to be clear and concise.
- Focus on technical learning.
- All content in natural Korean (**apply STEP 5 validation before upload — no exceptions**).