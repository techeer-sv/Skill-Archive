# Skill-Archive

Claude Code에서 사용할 수 있는 실용적인 스킬 모음집.

## Hiring Process Simulation

채용 프로세스 전 과정을 시뮬레이션하는 스킬셋. 서류 심사부터 코딩 테스트, 면접까지 경험하고 피드백을 받을 수 있습니다.

### 스킬 목록

| 스킬 | 명령어 | 설명 | 평가 체계 |
|------|--------|------|----------|
| 이력서 리뷰 | `/hiring-resume-review` | 이력서를 공고 기준으로 심층 리뷰 | 3축 평가 |
| 포트폴리오 리뷰 | `/hiring-portfolio-review` | 기술적 깊이와 의사결정 품질 평가 | 3축 평가 |
| 코딩 테스트 | `/hiring-coding-test` | 포지션 맞춤 코딩 테스트 시뮬레이션 | 점수제 |
| 면접 시뮬레이션 | `/hiring-interview` | 기술/인성/설계 면접 (실전+학습 모드) | 유형별 평가 |
| 통합 파이프라인 | `/hiring-full-pipeline` | 서류→코테→면접 전체 프로세스 | 종합 리포트 |

### 설치 방법

이 레포의 `skills/` 디렉토리 내용을 Claude Code 스킬 경로에 복사합니다:

```bash
# 전체 hiring 스킬셋 설치
cp -r skills/hiring-*/ ~/.claude/skills/

# 공통 모듈도 함께 복사 (필수)
cp -r skills/hiring-common/ ~/.claude/skills/
```

개별 스킬만 설치할 경우에도 공통 모듈은 필수입니다:

```bash
# 이력서 리뷰만 설치 (공통 모듈 필수)
cp -r skills/hiring-resume-review/ ~/.claude/skills/
cp -r skills/hiring-common/ ~/.claude/skills/
```

### 사용 예시

#### 이력서 리뷰만 받고 싶을 때
```
/hiring-resume-review
```
→ 이력서 경로 제공 → (선택) 공고 내용 붙여넣기 → 3축 평가 + 수정 제안

#### 면접 준비만 하고 싶을 때
```
/hiring-interview
```
→ 이력서 제공 → 유형 선택 → 모드 선택 (실전/학습) → 대화형 면접 → 피드백

#### 전체 채용 과정을 경험하고 싶을 때
```
/hiring-full-pipeline
```
→ 이력서 + 공고 → 서류 심사 → 코딩 테스트 → 기술 면접 → 인성 면접 → 종합 리포트

### 입력 형식

| 입력 | 필수 여부 | 지원 형식 |
|------|----------|----------|
| 이력서 | 대부분 필수 | PDF, Markdown, TXT |
| 포트폴리오 | 선택 | PDF, Markdown, 프로젝트 디렉토리 |
| 채용 공고 | 스킬에 따라 | 직접 붙여넣기 (권장), URL |

> DOCX는 PDF로 변환 후 제공하세요.
> 채용 공고 URL은 JS 렌더링 문제로 실패할 수 있어, 직접 붙여넣기를 권장합니다.

### 평가 프레임워크

스킬별로 **최적화된 평가 체계**를 사용합니다:

**이력서/포트폴리오** — First Principles 3축 평가:
1. **문제 인식** — 올바른 문제를 풀고 있는가?
2. **해결 과정** — 트레이드오프를 인식하고 합리적으로 접근했는가?
3. **성과/임팩트** — 의미 있는 변화를 만들었는가?

**코딩 테스트** — 고유 점수 체계 (정확성, 복잡도, 코드 품질, 문제 분해 능력)

**면접** — 유형별 평가 (기술: 깊이/의사결정, 인성: STAR, 설계: 아키텍처)

공통 판정: `PASS` / `NEEDS_WORK` / `REJECT`

상세: [`skills/hiring-common/evaluation-framework.md`](skills/hiring-common/evaluation-framework.md)

## 프로젝트 구조

```
skills/
  hiring-resume-review/SKILL.md     # 이력서 리뷰
  hiring-portfolio-review/SKILL.md  # 포트폴리오 리뷰
  hiring-coding-test/SKILL.md       # 코딩 테스트
  hiring-interview/SKILL.md         # 면접 시뮬레이션
  hiring-full-pipeline/SKILL.md     # 통합 파이프라인
  hiring-common/                    # 공통 모듈
    evaluation-framework.md         # 평가 프레임워크
    input-parser.md                 # 입력 파싱 가이드
  hiring-index.md                   # 스킬 인덱스
```
