---
name: hiring-full-pipeline
description: 채용 공고를 분석하여 해당 회사에 맞는 채용 프로세스를 시뮬레이션합니다. 회사별 맞춤 또는 일반 프로세스 지원.
user-invocable: true
---

# 통합 채용 파이프라인 (Hiring Full Pipeline)

회사별 맞춤 채용 프로세스를 시뮬레이션합니다. 채용 공고를 분석하여 해당 회사의 프로세스를 자동 감지하거나, 일반 프로세스(서류→코테→면접)로 진행합니다.
**어느 한 단계에서 REJECT되면 해당 단계에서 중단**하고 개선 피드백을 제공합니다.

---

## 핵심 규칙

```
1. 각 단계는 PASS / REJECT 이분법
2. REJECT 발생 시 즉시 중단 → 해당 단계 피드백 제공 → 개선 후 재도전 안내
3. 모든 단계 PASS 시에만 최종 합격
4. 회사 맞춤 프로세스는 사용자 확인 후 확정
```

> 각 단계 결과는 파일로 저장되므로 다른 세션에서 이어서 진행할 수 있습니다.

---

## 실행 절차

### Step 1: 입력 수집

먼저 `.hiring-pipeline/input-context.md`가 존재하는지 확인한다.
- **존재하면**: Read하여 이전 세션의 입력 정보를 복원. "이전 세션의 입력 정보를 발견했습니다. 그대로 사용할까요?" 확인. 또한 `.hiring-pipeline/` 내 stage 파일 존재 여부를 확인하여 완료된 단계를 자동 감지하고, 다음 미완료 단계부터 시작을 제안한다.
- **존재하지 않으면**: 아래 절차로 입력을 수집한다.

**이력서 (필수):**
- "이력서 파일 경로를 알려주세요. (PDF, Markdown, TXT)"
- 미제공 시 중단

**포트폴리오 (선택):**
- "포트폴리오가 있으면 파일 경로를 알려주세요. (없으면 '없음')"

**채용 공고 (필수):**
- "채용 공고 내용을 붙여넣어주세요. URL도 가능합니다."
- URL 제공 시 WebFetch 시도. 실패하면 직접 붙여넣기 요청.
- 미제공 시: "통합 파이프라인에서는 채용 공고가 필수입니다. 개별 스킬을 사용하세요: `/hiring-resume-review`, `/hiring-interview`"

**입력 컨텍스트 저장:**

입력 수집이 완료되면 즉시 저장한다:
```
Write 도구로 저장: .hiring-pipeline/input-context.md
```

저장 형식:
```markdown
## 입력 컨텍스트

**이력서 경로:** [절대 경로]
**포트폴리오 경로:** [절대 경로 또는 "없음"]
**채용 공고:** [붙여넣기 내용 또는 "job-analysis.md 참조"]
**생성 시각:** [ISO 타임스탬프]
```

### Step 2: 채용 공고 분석 + 회사 감지

#### 2a. 공고 분석

공고 내용을 분석하여 다음 형식으로 정리한다:

```markdown
## 채용 공고 분석

**회사:** [회사명]
**포지션:** [포지션명]
**경력:** [요구 경력]

### 필수 자격 요건 (Must-have)
- [요건]

### 우대 자격 요건 (Nice-to-have)
- [요건]

### 기술 스택
- [기술]

### 업무 내용
- [업무]
```

이 분석 결과를 사용자에게 보여주고 확인한다.

```
Write 도구로 저장: .hiring-pipeline/job-analysis.md
```

#### 2b. 회사 감지

공고에서 추출한 회사명을 기반으로 프로세스를 결정한다:

```
1. 공고 텍스트에서 회사명 추출
   - 회사명 불분명 시 → "이 공고의 회사명이 뭔가요?" 질문

2. Known Company DB 검색
   → Read: skills/hiring-common/company-profiles.md (또는 ~/.claude/skills/hiring-common/company-profiles.md)
   → 매칭된 회사 정보만 참조 (전체 DB를 컨텍스트에 올리지 않음)

   IF 매칭:
     → DB의 프로세스 + 특성 로드
     → "이 회사는 [회사명]으로 확인됩니다. [YYYY-MM] 기준 정보입니다."
     → "다음 프로세스로 진행합니다: [단계 목록]"
     → "이 정보는 참고용이며 실제와 다를 수 있습니다. 맞으면 '확인', 수정하려면 알려주세요."

3. ELSE 키워드 시그널 분석
   공고에서 다음 키워드를 탐지하여 프로세스를 추론:

   시그널 강도:
   - 강 (2점): 프로세스를 직접 명시 (예: "전형절차: 서류→코테→면접")
   - 중 (1점): 키워드로 암시 (예: "코딩 테스트 진행", "과제 전형")
   - 약 (0.5점): 간접 힌트 (예: "프로그래머스", "포트폴리오 우대")

   부정 표현: "코딩 테스트 없음", "알고리즘 테스트를 진행하지 않습니다" → 해당 단계 제외
   충돌: "코딩 테스트" + "과제 전형" 동시 → 둘 다 포함 (별도 단계)

   IF 시그널 합산 2점 이상:
     → 시그널 기반 프로세스 조합
     → "공고 내용을 분석한 결과 다음 프로세스로 추정합니다: [단계 목록]"

4. ELSE:
     → 일반 프로세스 (서류 → 코테 → 기술면접 → 인성면접)
     → "회사 정보를 특정할 수 없어 일반 프로세스로 진행합니다."
```

#### 2c. 프로세스 확인

```
항상: 사용자에게 수정 기회 제공
→ "단계를 추가/제거/변경하려면 말씀해주세요."
→ 사용자가 "확인" 시 → 프로세스 확정
```

#### 2d. 컨텍스트 파일 생성

사용자 확인 완료 후 company-context.md를 생성한다:

```
Write 도구로 저장: .hiring-pipeline/company-context.md
```

```markdown
## 회사 컨텍스트

**회사명:** [회사명]
**회사 유형:** [전통 대기업 / 유니콘·스케일업 / 스타트업 / 글로벌 빅테크]
**프로필 출처:** [known-db / keyword-inference / user-confirmed / general]
**최종 확인:** 사용자 확인 완료

### 채용 프로세스 (확정)
1. [단계명] — 담당: [스킬명:모드] 또는 "스킵"
2. [단계명] — 담당: [스킬명:모드] 또는 "스킵"
...

### 회사 특성
- **평가 중점:** [알고리즘/실무/과제/CS기초/컬처핏 중 해당 항목]
- **면접 스타일:** [구조화/대화형/패널/압박]
- **고유 가치:** [리스트. 예: Customer Obsession, Ownership, DRI]
- **코딩 테스트 플랫폼:** [프로그래머스/HackerRank/Codility/자체/없음]
- **특이사항:** [블라인드 채용, Bar Raiser, 과제 후 코드리뷰 면접 등]

### 참고 (DB 출처 시)
- **DB 기준일:** [YYYY-MM]
- **주의:** 이 정보는 참고용이며 실제 프로세스와 다를 수 있습니다.
```

### Step 3: 파이프라인 실행

company-context.md의 채용 프로세스 리스트를 순회하며 각 단계를 실행한다.

**프로세스 → 스킬 매핑:**

| 단계명 | 스킬:모드 |
|--------|----------|
| 서류 심사 | resume-review:default |
| 포트폴리오 리뷰 | portfolio-review:default |
| 알고리즘 코테 | coding-test:algorithm |
| 시스템 설계 코테 | coding-test:system-design |
| 실무 코딩 | coding-test:practical |
| 사전 과제 | coding-test:take-home |
| 기술 면접 | interview:technical |
| 인성 면접 | interview:behavioral |
| 회사 가치 면접 | interview:company-values |
| CS 필기/퀴즈 | interview:technical (CS Q&A 집중: 질문의 80%를 CS 기초로 구성) |
| 리크루터 콜 | SKIP + 팁 제공 |
| Hiring Committee | SKIP + 설명 |

**실행 루프:**

```
stages = company-context.md의 채용 프로세스 리스트

for each stage in stages:

  if stage == "스킵":
    → "[단계명]은 시뮬레이션이 어려운 단계입니다 (사유: [비대화형/내부 프로세스]).
       이 단계는 건너뛰고, 실제 지원 시 참고할 정보를 안내합니다."
    → 해당 단계에 대한 실전 팁 제공
    → Write: .hiring-pipeline/stage{N}-skip-{stage_name}.md
    → continue

  → 해당 스킬의 SKILL.md를 Read (프로젝트 내 또는 ~/.claude/skills/ 경로)
  → 해당 스킬의 절차를 수행 (입력은 이미 수집됨)

  컨텍스트 활용:
  - 이전 단계 결과 파일을 Read하여 참조
  - 서류에서 파악한 기술 수준 → 코테/면접 난이도 자동 결정
  - 코테 풀이 방식 → 기술 면접 딥다이브 대상
  - take-home PASS 시 → 다음 기술 면접에서 코드 리뷰 질문 50% 포함
  - company-context.md의 회사 특성 → 면접 스타일/평가 기준에 반영

  → Write: .hiring-pipeline/stage{N}-{skill_short}-{mode}.md

  if result == REJECT:
    → 중단 리포트 작성 → BREAK

  → "다음 단계([단계명])로 진행합니다." 안내
```

**스테이지 파일 네이밍 규칙:**
- `stage{N}-{skill_short}-{mode}.md`
- skill_short: `resume` / `portfolio` / `coding` / `interview`
- 예시 (토스): stage1-resume-default.md, stage2-coding-take-home.md, stage3-interview-technical.md, stage4-interview-company-values.md
- 예시 (카카오): stage1-resume-default.md, stage2-coding-algorithm.md, stage3-coding-algorithm.md, stage4-interview-technical.md, stage5-interview-behavioral.md

**포트폴리오 처리:**
- 서류 심사 단계에서 포트폴리오가 제공된 경우:
  - `hiring-portfolio-review/SKILL.md`도 Read하여 함께 수행
  - 이력서 PASS + 포트폴리오 PASS → 서류 심사 PASS
  - 이력서 REJECT → 서류 심사 REJECT (포트폴리오 무관)
  - 이력서 PASS + 포트폴리오 REJECT → 서류 심사 PASS (포트폴리오는 보조 자료)

### Step 4: 최종 결과

#### 모든 단계 PASS 시 → 종합 리포트 작성

모든 stage 파일을 Read하여 종합 리포트를 작성한다.

```markdown
# 채용 프로세스 종합 리포트

## 최종 판정: PASS (전 단계 통과)

## 지원 정보
- **회사:** [회사명]
- **포지션:** [포지션명]
- **경력 수준:** [주니어/미드/시니어]
- **프로세스 유형:** [회사 맞춤 / 시그널 추론 / 일반]

## 단계별 결과

| 단계 | 판정 | 핵심 |
|------|------|------|
| [단계명] | PASS | [1줄 요약] |
| [단계명] | PASS | [1줄 요약] |
| ... | ... | ... |

## 강점 Top 3
1. [강점]: [어느 단계에서 확인, 구체적 근거]
2. [강점]: [근거]
3. [강점]: [근거]

## 개선 권장 사항
1. [개선점]: [근거] → [방법]
2. [개선점]: [근거] → [방법]

## 포지션 적합도

| 필수 요건 | 충족 | 확인 단계 |
|----------|------|----------|
| [요건] | O/X/△ | [어느 단계에서 확인] |

### 종합 적합도: [매우 적합 / 적합 / 보통]
```

#### REJECT으로 중단 시 → 중단 리포트 작성

```markdown
# 채용 프로세스 중단 리포트

## 중단 단계: [단계명] — REJECT
## 완료 단계: [X] / [전체 단계 수]

## 단계별 결과

| 단계 | 판정 | 핵심 |
|------|------|------|
| [완료 단계] | PASS | [요약] |
| [중단 단계] | REJECT | [탈락 사유] |
| [미진행 단계] | - | 미진행 |

## 탈락 사유 상세
[중단 단계의 상세 피드백]

## 재도전 가이드

### 이 단계를 통과하려면
- [구체적 개선 항목 1]: [방법]
- [구체적 개선 항목 2]: [방법]

### 추천 학습 자원
- [개선점에 맞는 학습 방향]

### 재도전 방법
"개선 후 `/hiring-full-pipeline`으로 다시 시작하세요.
이전 단계 PASS 결과는 `.hiring-pipeline/`에 저장되어 있습니다."
```

종합 리포트 저장:
```
Write 도구로 저장: .hiring-pipeline/final-report.md
```

---

## 파일 기반 상태 관리

```
.hiring-pipeline/
  input-context.md                      # 입력 정보 (이력서 경로, 포트폴리오 경로, 공고)
  job-analysis.md                       # 공고 분석 결과
  company-context.md                    # 회사 컨텍스트 (프로세스, 특성, 가치)
  stage{N}-{skill_short}-{mode}.md      # 각 단계 결과 (동적 네이밍)
  stage{N}-skip-{stage_name}.md         # 스킵된 단계 (팁 포함)
  final-report.md                       # 종합/중단 리포트
```

## 주의사항

- REJECT 시 반드시 "왜 탈락인지" + "무엇을 어떻게 고치면 통과 가능한지" 제공
- 각 단계는 개별 스킬과 동일한 품질로 수행된다
- REJECT 후 강제 진행은 없다. 개선 후 재도전을 안내한다
- Known Company DB의 정보는 참고용이다. "이 정보는 [YYYY-MM] 기준이며 변경되었을 수 있습니다" 고지
- 사용자가 프로세스를 수정하면 즉시 반영한다
- 전체 파이프라인은 상당한 시간이 소요된다. 단계별로 나눠서 진행하는 것을 권장한다
