# 에이전트별 사용

[가이드 홈](index.md) · [시작하기](getting-started.md)

이 프로젝트에서 배포·사용하는 스킬은 **`yuriring-game-system-design`** 하나입니다. 실제 스킬 원본은 `.agents/skills/yuriring-game-system-design/`에 있고, Claude Code용 경로는 그 원본을 가리키는 개별 링크입니다.

> [!NOTE]
> **파일 배치와 앱 탐색은 다른 확인입니다**
>
> 아래 파일·링크는 현재 프로젝트에서 확인한 사실입니다. 각 앱의 명령 메뉴나 설정 화면에서 스킬을 실제로 탐색·호출하는 동작은 이 문서 작성 시 실행 검증하지 않았습니다.

---

## 실제 프로젝트 구성

```text
README.md
docs/
  index.md, getting-started.md, agents.md, workflow.md,
  advanced-usage.md, validation.md, troubleshooting.md
.agents/skills/
  yuriring-game-system-design/
    SKILL.md
    chapters/                  # 21개 장
    references/index.md        # 사례·질문별 참조 자료 색인
    glossary.md, patterns.md, cheatsheet.md, source.json
.claude/skills/
  yuriring-game-system-design -> ../../.agents/skills/yuriring-game-system-design
```

| 경로 | 역할 | 확인 방법 |
|---|---|---|
| `.agents/skills/yuriring-game-system-design/SKILL.md` | 스킬 본문과 장 색인 | 파일 존재와 frontmatter 확인 |
| `.agents/skills/yuriring-game-system-design/chapters/` | 21개 장별 자료 | 장 파일과 색인 링크 확인 |
| `.agents/skills/yuriring-game-system-design/references/index.md` | 사례·질문별 참조 자료 선택 | 색인에서 실제 장·보조 자료로 이동되는지 확인 |
| `.claude/skills/yuriring-game-system-design` | Claude Code가 읽을 프로젝트 경로 | 링크 대상이 원본 폴더인지 확인 |

> [!IMPORTANT]
> **실제 호출 이름**
>
> 스킬 호출 ID는 `yuriring-game-system-design`입니다. `yuriring-game-design`은 프로젝트 이름입니다. 대화창에서는 스킬 호출 ID를 사용하고, 프로젝트를 설명할 때는 프로젝트 이름을 사용합니다.

---

## Codex

프로젝트를 연 뒤 대화창에서 호출합니다.

```text
$yuriring-game-system-design 길드 기부 시스템의 규칙과 예외를 검토해줘.
```

특정 장을 우선 적용하고 싶다면 장 번호와 원하는 산출물을 함께 적습니다.

```text
$yuriring-game-system-design 10~12장을 기준으로
이벤트 보상 테이블의 키·참조·중복 지급 방지 조건을 설계해줘.
```

---

## Claude Code

프로젝트 루트에서 Claude Code를 시작한 뒤 호출합니다.

```text
/yuriring-game-system-design 길드 기부 시스템의 규칙과 예외를 검토해줘.
```

스킬이 메뉴에 보이지 않으면 다음 순서로 확인합니다.

1. `.claude/skills/yuriring-game-system-design` 링크가 존재하는지 확인합니다.
2. 링크 대상에 `SKILL.md`가 있는지 확인합니다.
3. 프로젝트 루트에서 새 세션을 시작해 다시 탐색합니다.

> [!TIP]
> **실제 링크 확인 예시**
>
> `ls -l .claude/skills/yuriring-game-system-design` 결과의 대상이 `../../.agents/skills/yuriring-game-system-design`이면 현재 프로젝트 배치와 일치합니다.

---

## Antigravity

프로젝트를 작업공간으로 열고 스킬 이름을 포함한 자연어 요청을 사용합니다.

```text
yuriring-game-system-design 스킬을 사용해
모바일 수집형 RPG의 길드 기부 기능을 설계해줘.
규칙·예외·데이터·UI 흐름과 미결정 정책을 구분해줘.
```

프로젝트 안의 `.agents/skills/yuriring-game-system-design/` 경로가 스킬 원본입니다. Antigravity의 실제 탐색 결과, UI 항목 이름, 명령 입력 방식은 사용 중인 앱 버전과 설정에서 별도로 확인하세요.

> [!WARNING]
> **파일이 있어도 앱에서 즉시 보인다고 단정하지 마세요**
>
> 프로젝트 배치를 확인한 것과 실행 중인 앱이 스킬을 로드한 것은 서로 다른 증거입니다. 앱에서 이름을 찾을 수 없다면 작업공간 경로와 앱의 스킬 설정을 먼저 확인하고, 그 결과를 바탕으로 문제를 좁히세요.

---

## 공통 요청 형식

세 도구에서 같은 내용으로 요청할 수 있습니다. 호출 표기만 각 도구에 맞게 바꿉니다.

```text
[스킬 호출] [기능 또는 문서]
목표: [바꾸려는 플레이 경험 또는 해결할 문제]
맥락: [장르·대상 이용자·기존 기능]
제약: [일정·플랫폼·기술·운영 조건]
원하는 결과: [설계 / 검토 / 테이블 / UI 명세]
미정 정책은 ‘결정이 필요한 사항’ 표로 분리해줘.
```

호출 이후의 장별 설계 전략은 [고급 활용법](advanced-usage.md), 답변 검수는 [검수와 테스트](validation.md)를 사용하세요.
