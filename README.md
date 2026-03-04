# Skill-Archive

Claude Code에서 사용할 수 있는 실용적인 스킬 모음집.

## Hiring Process Simulation

실제 기업 채용 프로세스를 기반으로 한 시뮬레이션 스킬셋. ATS 스크리닝, FAANG 스타일 코딩 테스트 루브릭, 구조화 면접 평가를 경험할 수 있습니다.

### 스킬 목록

| 스킬 | 명령어 | 설명 |
|------|--------|------|
| 이력서 리뷰 | `/hiring-resume-review` | ATS 시뮬레이션 + 리크루터 스크리닝 |
| 포트폴리오 리뷰 | `/hiring-portfolio-review` | 프로젝트 품질과 기술적 의사결정 평가 |
| 코딩 테스트 | `/hiring-coding-test` | 4차원 루브릭 (문제해결/코딩/커뮤니케이션/테스트) |
| 면접 시뮬레이션 | `/hiring-interview` | 기술 면접 (6단계 Hire 스케일) + 인성 면접 (STAR) |
| 통합 파이프라인 | `/hiring-full-pipeline` | 서류→코테→면접 전체 (REJECT 시 즉시 중단) |

### 설치 방법

```bash
# 전체 hiring 스킬셋 설치
cp -r skills/hiring-*/ ~/.claude/skills/

# 공통 모듈도 함께 복사 (필수)
cp -r skills/hiring-common/ ~/.claude/skills/
```

### 사용 예시

#### 이력서 리뷰
```
/hiring-resume-review
```
→ 이력서 경로 제공 → 공고 붙여넣기 → ATS 점수 + 리크루터 스크리닝 + 수정 제안

#### 전체 채용 과정
```
/hiring-full-pipeline
```
→ 이력서 + 공고 → 서류 심사(ATS) → 코딩 테스트 → 기술 면접 → 인성 면접
→ 어느 단계든 REJECT 시 중단 + 개선 피드백

### 판정 체계

**PASS / REJECT 이분법.** 실제 채용처럼 통과 아니면 탈락.
REJECT 시 "왜 탈락인지" + "무엇을 어떻게 고치면 통과 가능한지" 반드시 제공.

| 단계 | 판정 기준 |
|------|----------|
| 서류 심사 | ATS 점수 70+ AND 필수 자격 충족 AND Red Flag 없음 |
| 코딩 테스트 | 4차원 모두 2점+ AND 평균 2.5+ (1~4점 스케일) |
| 기술 면접 | Leaning Hire 이상 (6단계 스케일) |
| 인성 면접 | 5개 역량 모두 2점+ AND 평균 3+ (1~5점 스케일) |

### 입력 형식

| 입력 | 필수 여부 | 지원 형식 |
|------|----------|----------|
| 이력서 | 대부분 필수 | PDF, Markdown, TXT |
| 포트폴리오 | 선택 | PDF, Markdown, 프로젝트 디렉토리 |
| 채용 공고 | 통합 파이프라인 필수 | 직접 붙여넣기 (권장), URL |

> DOCX는 PDF로 변환 후 제공하세요.

## 프로젝트 구조

```
skills/
  hiring-resume-review/SKILL.md     # 이력서 리뷰 (ATS + 리크루터)
  hiring-portfolio-review/SKILL.md  # 포트폴리오 리뷰
  hiring-coding-test/SKILL.md       # 코딩 테스트 (4차원 루브릭)
  hiring-interview/SKILL.md         # 면접 (기술 + 인성)
  hiring-full-pipeline/SKILL.md     # 통합 파이프라인
  hiring-common/                    # 공통 모듈
    evaluation-framework.md         # 평가 프레임워크
    input-parser.md                 # 입력 파싱 가이드
  hiring-index.md                   # 스킬 인덱스
```
