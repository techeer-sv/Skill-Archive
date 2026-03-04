# Hiring Process Simulation Skills

채용 프로세스 전 과정을 시뮬레이션하는 스킬 모음.

## 스킬 목록

| 스킬 | 호출 | 설명 | 필수 입력 | 평가 체계 |
|------|------|------|----------|----------|
| [이력서 리뷰](hiring-resume-review/SKILL.md) | `/hiring-resume-review` | 이력서 심층 리뷰 | 이력서 | 3축 평가 (존재 여부) |
| [포트폴리오 리뷰](hiring-portfolio-review/SKILL.md) | `/hiring-portfolio-review` | 포트폴리오 평가 | 포트폴리오 | 3축 평가 (품질) |
| [코딩 테스트](hiring-coding-test/SKILL.md) | `/hiring-coding-test` | 코딩 테스트 시뮬레이션 | 포지션 정보 | 고유 점수제 |
| [면접 시뮬레이션](hiring-interview/SKILL.md) | `/hiring-interview` | 기술/인성/설계 면접 | 이력서 | 유형별 고유 체계 |
| [통합 파이프라인](hiring-full-pipeline/SKILL.md) | `/hiring-full-pipeline` | 전체 프로세스 | 이력서 + 공고 | 단계별 종합 |

## 공통 모듈

| 모듈 | 설명 |
|------|------|
| [평가 프레임워크](hiring-common/evaluation-framework.md) | First Principles 기반 3축 평가 체계 |
| [입력 파싱 가이드](hiring-common/input-parser.md) | 이력서/포트폴리오/공고 입력 처리 |

## 평가 체계

스킬별로 차등 적용:
- **이력서/포트폴리오**: First Principles 3축 평가 (문제 인식/해결 과정/성과)
- **코딩 테스트**: 고유 점수 체계 (정확성, 복잡도, 코드 품질 등)
- **면접**: 유형별 고유 체계 (기술: 깊이/의사결정, 인성: STAR, 설계: 아키텍처)
- **통합 파이프라인**: 단계별 결과 종합

공통 판정: `PASS` / `NEEDS_WORK` / `REJECT`
