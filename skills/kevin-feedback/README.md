# kevin-feedback 스킬

**Kevin의 말투로 이력서 피드백**을 제공하는 스킬입니다.

Kevin의 Slack 메시지를 수집·분석하여 만든 스타일 프로파일을 기반으로, 실제로 그 사람처럼 이력서를 리뷰해 드립니다.

---

## 사용 방법

이력서 파일 경로를 함께 입력하시면 됩니다.

```
/kevin-feedback [파일경로]
/kevin-feedback ~/Documents/my-resume.md
/kevin-feedback ./resume.pdf
```

별도 설정 없이 바로 사용 가능합니다.

---

## 파일 구조

```
kevin-feedback/
├── SKILL.md                        # 스킬 실행 흐름 정의
├── README.md                       # 이 파일
└── references/
    ├── style-analysis-guide.md     # 말투 분석 기준
    ├── feedback-structure.md       # 피드백 출력 구조
    └── learned/
        └── kevin-style-profile.md  # Kevin 스타일 프로파일 (본인 동의 하에 공개)
```

---

## 출력 예시

```
## 이력서 피드백 by Kevin

**첫인상**
문제 정의까지는 잘 됐는데 "그래서 얼마나 좋아졌는지" 결과 수치가
좀 더 필요한 이력서인 것 같음 호호

**경험 / 프로젝트**
- 인턴: 기술 도입 이유가 명확하게 써있어서 인상적임~
  근데 기술 도입 후 실제 결과 수치가 없어서 아쉬운 것 같음

**이렇게 고쳐봐**
1. 기술 결과 수치 추가 → "중복 매칭 0건" 같은 증거물 넣기
2. 기술 90% 수치 근거 명확히 → 측정 방법 한 줄 추가

**총평**
수치 보강하면 꽤 임팩트 있는 이력서가 될 것 같음~ 조금만 더 다듬어봐요!
```

---

## 사전 조건

- **Claude Code** 환경
