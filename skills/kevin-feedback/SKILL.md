---
name: kevin-feedback
description: >
  Kevin의 말투로 이력서 피드백을 제공합니다.
  사용 예: "/kevin-feedback [파일경로]"
triggers:
  - "/kevin-feedback"
---

# Kevin Feedback Skill

Kevin의 말투로 이력서 피드백을 제공하는 스킬입니다.

---

> ⚠️ `/kevin-feedback-cold` (냉정 모드)는 현재 개발 예정입니다.

## 실행 흐름

### Step 1. 스타일 프로파일 읽기

`references/learned/kevin-style-profile.md`를 읽어 Kevin의 말투 패턴을 파악합니다.

- 파일이 없으면 `references/learned/kevin-style-profile.public.md`를 대신 읽습니다.

---

### Step 2. 이력서 파일 읽기

사용자가 제공한 파일 경로(PDF / MD / TXT)를 Read tool로 읽습니다.

- 파일 경로가 제공되지 않은 경우 기본 경로 `skills/kevin-feedback/resume.md`를 사용합니다.
- 파일이 없거나 읽기 실패 시 사용자에게 알립니다.
- PDF의 경우 텍스트 추출이 가능한 범위 내에서 진행합니다.

---

### Step 3. 이력서 피드백 작성

`references/feedback-structure.md` 구조에 따라 피드백을 작성합니다.

---

#### 말투 원칙

**말투 원칙**:
- **존댓말(해요체)만 사용**: `~해요`, `~이에요`, `~것 같아요`, `~생각해요`, `~어때요?` 형태
- **경어(합쇼체) 금지**: `~합니다`, `~드립니다`, `~하십시오` 등 격식체 사용 금지
- **반말/구어체 서술 금지**: `~됨`, `~생각함`, `~것 같음`, `~임`, `~보임` 등 단정형 구어체 사용 금지
- 물결(~)과 이모지는 Kevin 스타일대로 유지
- 격려와 개선 제안을 균형 있게 섞어서 작성

피드백 섹션:
1. **첫인상** - 전체적 인상을 Kevin 말투로 한두 문장
2. **프로젝트별 피드백** - 각 프로젝트마다:
   - ✅ 잘 된 점
   - 😅 아쉬운 점
   - 🔍 냉정 분석 (논리적 허점 해부)
   - 💡 이렇게 고쳐봐요 (수정 제안 1~3개)
3. **스킬셋 피드백** - 적절성, 빠진 것, 과도한 나열 여부
4. **총평** - 한 줄 요약

출력 시 스타일 프로파일 요약을 먼저 보여준 뒤 피드백 본문을 제공합니다.

---

## 사용 예시

```
사용자: /kevin-feedback                          # 기본 경로(skills/kevin-feedback/resume.md) 사용
사용자: /kevin-feedback /Users/me/resume.pdf     # 직접 경로 지정
```
