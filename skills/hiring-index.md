# Hiring Process Simulation Skills

채용 프로세스 전 과정을 시뮬레이션하는 스킬 모음. 실제 기업 채용 프로세스를 기반으로 설계.

## 스킬 목록

| 스킬 | 호출 | 설명 | 평가 체계 |
|------|------|------|----------|
| [이력서 리뷰](hiring-resume-review/SKILL.md) | `/hiring-resume-review` | ATS 시뮬레이션 + 리크루터 스크리닝 | ATS 점수(0~100) + Red Flag 체크 |
| [포트폴리오 리뷰](hiring-portfolio-review/SKILL.md) | `/hiring-portfolio-review` | 프로젝트 품질과 의사결정 평가 | 3영역 평가 (의사결정/문제해결/성과) |
| [코딩 테스트](hiring-coding-test/SKILL.md) | `/hiring-coding-test` | FAANG 스타일 코딩 테스트 | 4차원 루브릭 (1~4점) |
| [면접 시뮬레이션](hiring-interview/SKILL.md) | `/hiring-interview` | 기술/인성 면접 | 6단계 Hire 스케일 / STAR 5점 |
| [통합 파이프라인](hiring-full-pipeline/SKILL.md) | `/hiring-full-pipeline` | 전체 프로세스 (REJECT 시 중단) | 단계별 PASS/REJECT |

## 공통 모듈

| 모듈 | 설명 |
|------|------|
| [평가 프레임워크](hiring-common/evaluation-framework.md) | ATS, FAANG 루브릭, STAR 등 실전 평가 기준 |
| [입력 파싱 가이드](hiring-common/input-parser.md) | 이력서/포트폴리오/공고 입력 처리 |

## 판정 체계

모든 단계: `PASS` / `REJECT` (이분법)
- REJECT 시 구체적 개선 피드백 필수 제공
- 통합 파이프라인에서 REJECT 발생 시 즉시 중단
