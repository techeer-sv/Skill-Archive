---
name: hiring-full-pipeline
description: 서류 심사부터 코딩 테스트, 면접까지 전체 채용 프로세스를 시뮬레이션합니다.
user-invocable: true
---

# 통합 채용 파이프라인 (Hiring Full Pipeline)

서류 심사 → 코딩 테스트 → 기술 면접 → 인성 면접까지 전체 채용 프로세스를 시뮬레이션합니다.
**어느 한 단계에서 REJECT되면 해당 단계에서 중단**하고 개선 피드백을 제공합니다.

---

## 핵심 규칙

```
1. 각 단계는 PASS / REJECT 이분법
2. REJECT 발생 시 즉시 중단 → 해당 단계 피드백 제공 → 개선 후 재도전 안내
3. 모든 단계 PASS 시에만 최종 합격
```

```
[서류 심사] →PASS→ [코딩 테스트] →PASS→ [기술 면접] →PASS→ [인성 면접] →PASS→ 최종 합격
     ↓REJECT           ↓REJECT            ↓REJECT           ↓REJECT
   중단+피드백       중단+피드백         중단+피드백        중단+피드백
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

### Step 2: 채용 공고 분석

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

### Step 3: 파이프라인 실행

각 단계를 순차적으로 실행한다. **REJECT 발생 시 즉시 중단.**

---

#### 단계 1: 서류 심사

다음 파일을 Read 도구로 읽고 해당 절차를 수행한다:
- 프로젝트 내: `skills/hiring-resume-review/SKILL.md`
- 설치 환경: `~/.claude/skills/hiring-resume-review/SKILL.md`

> 두 경로 중 존재하는 쪽을 사용한다.

**수행 범위:** SKILL.md의 Step 2~6 (입력은 이미 수집됨)

**포트폴리오 제공 시:**
- `hiring-portfolio-review/SKILL.md`도 Read하여 함께 수행
- 이력서 PASS + 포트폴리오 PASS → 서류 심사 PASS
- 이력서 REJECT → 서류 심사 REJECT (포트폴리오 무관)
- 이력서 PASS + 포트폴리오 REJECT → 서류 심사 PASS (포트폴리오는 보조 자료)

**결과 저장:**
```
Write 도구로 저장: .hiring-pipeline/stage1-resume-review.md
```

**판정:**
- `PASS` → "서류 심사를 통과했습니다. 다음 단계(코딩 테스트)로 진행합니다."
- `REJECT` → 중단. 개선 피드백 제공 후:
  - "피드백을 반영하여 이력서를 수정한 후 `/hiring-full-pipeline`으로 재도전하세요."

---

#### 단계 2: 코딩 테스트

이전 단계 결과 참조:
```
Read: .hiring-pipeline/stage1-resume-review.md
Read: .hiring-pipeline/job-analysis.md
```

다음 스킬을 Read하여 수행 (프로젝트 내 또는 `~/.claude/skills/` 경로):
- `hiring-coding-test/SKILL.md`

**파이프라인 기본 옵션 (자동 적용):**
- 유형: "자동 선택" (서류 심사에서 파악한 포지션 기반)
- 난이도: "자동" (서류 심사에서 파악한 경력 수준 기반)

**컨텍스트 활용:** 서류 심사에서 파악한 기술 수준으로 난이도/유형 자동 결정

**결과 저장:**
```
Write 도구로 저장: .hiring-pipeline/stage2-coding-test.md
```

**판정:**
- `PASS` → "코딩 테스트를 통과했습니다. 다음 단계(기술 면접)로 진행합니다."
- `REJECT` → 중단. 개선 피드백 + 모범 답안 제공.

---

#### 단계 3: 기술 면접

이전 단계 결과 참조:
```
Read: .hiring-pipeline/stage1-resume-review.md
Read: .hiring-pipeline/stage2-coding-test.md
Read: .hiring-pipeline/job-analysis.md
```

다음 스킬을 Read하여 수행 (프로젝트 내 또는 `~/.claude/skills/` 경로):
- `hiring-interview/SKILL.md` (기술 면접 유형으로 실행)

**파이프라인 기본 옵션:**
- 면접 유형: "기술 면접"
- 진행 모드: "학습 모드"

**컨텍스트 활용:**
- 서류에서 발견된 모호한 점 → 면접 딥다이브 대상
- 코딩 테스트 풀이 방식 → 기술적 깊이 질문에 반영

**결과 저장:**
```
Write 도구로 저장: .hiring-pipeline/stage3-tech-interview.md
```

**판정:**
- `PASS` (Leaning Hire 이상) → "기술 면접을 통과했습니다. 최종 단계(인성 면접)로 진행합니다."
- `REJECT` (Leaning Reject 이하) → 중단. 상세 피드백 제공.

---

#### 단계 4: 인성/컬처핏 면접

이전 단계 결과 참조:
```
Read: .hiring-pipeline/stage1-resume-review.md
Read: .hiring-pipeline/stage3-tech-interview.md
Read: .hiring-pipeline/job-analysis.md
```

다음 스킬을 Read하여 수행 (프로젝트 내 또는 `~/.claude/skills/` 경로):
- `hiring-interview/SKILL.md` (인성/컬처핏 면접 유형으로 실행)

**파이프라인 기본 옵션:**
- 면접 유형: "인성/컬처핏 면접"
- 진행 모드: "학습 모드"

**컨텍스트 활용:**
- 이전 단계에서 드러난 협업/커뮤니케이션 스타일 검증
- 회사 문화와의 적합성 평가 (JD 기반)

**결과 저장:**
```
Write 도구로 저장: .hiring-pipeline/stage4-culture-interview.md
```

**판정:**
- `PASS` → 최종 합격으로 진행
- `REJECT` → 중단. 상세 피드백 제공.

---

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

## 단계별 결과

| 단계 | 판정 | 핵심 |
|------|------|------|
| 서류 심사 | PASS | ATS [X]점, [1줄 요약] |
| 코딩 테스트 | PASS | 평균 [X]/4.0, [1줄 요약] |
| 기술 면접 | PASS | [Hire 판정], [1줄 요약] |
| 인성 면접 | PASS | 평균 [X]/5.0, [1줄 요약] |

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
## 완료 단계: [X] / 4

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
  input-context.md             # 입력 정보 (이력서 경로, 포트폴리오 경로, 공고)
  job-analysis.md              # 공고 분석 결과
  stage1-resume-review.md      # 서류 심사 결과
  stage2-coding-test.md        # 코딩 테스트 결과
  stage3-tech-interview.md     # 기술 면접 결과
  stage4-culture-interview.md  # 인성 면접 결과
  final-report.md              # 종합/중단 리포트
```

## 주의사항

- REJECT 시 반드시 "왜 탈락인지" + "무엇을 어떻게 고치면 통과 가능한지" 제공
- 각 단계는 개별 스킬과 동일한 품질로 수행된다
- REJECT 후 강제 진행은 없다. 개선 후 재도전을 안내한다
- 전체 파이프라인은 상당한 시간이 소요된다. 단계별로 나눠서 진행하는 것을 권장한다
