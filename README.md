# 유리링 게임 시스템 기획 스킬

정윤지(유리링)의 『유리링의 실전 게임 시스템 기획』 **8부 21장**을 바탕으로 개념 학습·시스템 설계·기획서 검토에 사용하는 프로젝트 스킬입니다. 실제 호출 이름은 **`yuriring-game-system-design`**입니다.

## TOC

- [설치하기](#설치하기)
  - [프롬프트로 설치](#프롬프트로-설치)
  - [CLI로 수동 설치](#cli로-수동-설치)
- [시작하기](#시작하기)
- [간단한 사용법과 예시](#간단한-사용법과-예시)
  - [내 상황에 맞게 요청하기](#내-상황에-맞게-요청하기)
- [스킬 구성과 작동 방식](#스킬-구성과-작동-방식)
  - [참조 자료 선택](#참조-자료-선택)
  - [책의 전체 21장 구성](#책의-전체-21장-구성)
- [입력과 준비 상태](#입력과-준비-상태)
- [상세 가이드](#상세-가이드)
  - [고급 활용법 — 장별 프롬프트와 실무 시나리오](docs/advanced-usage.md)
- [저작권과 공유](#copyright)
- [프로젝트 정보](#프로젝트-정보)

---

## 설치하기

> [!NOTE]
> **현재 프로젝트에서는 바로 사용합니다**
>
> 이 프로젝트에는 게임 기획 스킬과 Claude Code 연결이 이미 있습니다. [시작하기](#시작하기)로 이동하세요. 아래는 **다른 프로젝트에 이 스킬을 설치할 때** 사용하는 방법입니다.

### 프롬프트로 설치

대상 프로젝트를 에이전트에서 열고 **대화창**에 입력합니다. `소스`에는 이 프로젝트 폴더의 실제 절대 경로를 적습니다.

```text
다음 소스의 yuriring-game-system-design 스킬만 현재 프로젝트 범위로 설치해줘.
소스: <이 프로젝트 폴더의 절대 경로>
.agents/skills/yuriring-game-system-design 폴더 전체를 현재 프로젝트의 같은 경로로 복사해줘.
Claude Code용 .claude/skills/yuriring-game-system-design에는 같은 원본을 가리키는 상대 링크를 만들어줘.
기존 설치는 덮어쓰지 말고, 원본과 링크에서 SKILL.md를 읽을 수 있는지 확인해줘.
```

### CLI로 수동 설치

macOS·Linux·WSL의 POSIX 셸 기준입니다. **대상 프로젝트 루트**에서 실행하고, `SOURCE_DIR`을 원본 프로젝트의 실제 절대 경로로 바꿉니다. 기존 경로는 덮어쓰지 않습니다.

```bash
SOURCE_DIR='/원본/프로젝트의/절대경로'
SKILL_NAME='yuriring-game-system-design'
mkdir -p .agents/skills .claude/skills
if [ ! -e ".agents/skills/$SKILL_NAME" ] && [ ! -L ".agents/skills/$SKILL_NAME" ]; then
  cp -R "$SOURCE_DIR/.agents/skills/$SKILL_NAME" .agents/skills/
fi
if [ ! -e ".claude/skills/$SKILL_NAME" ] && [ ! -L ".claude/skills/$SKILL_NAME" ]; then
  ln -s "../../.agents/skills/$SKILL_NAME" ".claude/skills/$SKILL_NAME"
fi
test -r ".agents/skills/$SKILL_NAME/SKILL.md"
test -r ".claude/skills/$SKILL_NAME/SKILL.md"
```

> [!IMPORTANT]
> **스킬 폴더 전체가 필요합니다**
>
> `SKILL.md`와 함께 `chapters/`, `references/`, 용어집·패턴·판단표, 출처 기록을 복사합니다. 기존 폴더가 있으면 위 명령은 그대로 보존하며 업데이트하지 않습니다. 설치 후 새 세션에서 실제 스킬 인식을 확인하세요.

---

## 시작하기

**이 프로젝트를 Codex·Claude Code·Antigravity에서 열고, 대화창에서 호출하세요.**

| 호스트 | 바로 사용할 요청 |
|---|---|
| Codex | `$yuriring-game-system-design 길드 기부 시스템의 의도·규칙·예외·데이터·UX/UI를 연결해 설계해줘.` |
| Claude Code | `/yuriring-game-system-design 길드 기부 시스템의 의도·규칙·예외·데이터·UX/UI를 연결해 설계해줘.` |
| Antigravity | `yuriring-game-system-design 스킬을 사용해 길드 기부 시스템의 의도·규칙·예외·데이터·UX/UI를 연결해 설계해줘.` |

> [!TIP]
> **기본 질문에는 원문 PDF나 Python이 필요하지 않습니다**
>
> 포함된 장별 지식 파일을 읽어 학습·설계·검토에 적용합니다. 중요한 수치·도식·인용을 원문과 재확인할 때는 해당 원본 자료가 필요합니다.

구체적인 요청에는 **장르·대상 플레이어·해결할 문제·기존 시스템·확정 정책·미정 정책·원하는 산출물**을 덧붙이세요.

```text
$yuriring-game-system-design 참조 색인에서 필요한 장을 골라 길드 기부 시스템을 설계해줘.
가상 전제: 협동 RPG에서 기부하면 개인 기여도와 길드 진행도가 증가한다.
확정 정책: 같은 요청으로 기여도가 중복 반영되면 안 된다.
미정 정책: 기부 한도, 취소 가능 여부, 탈퇴 후 보상 수령.
기능·속성, 규칙·예외, 데이터 관계, UI 상태, QA 시나리오로 정리해줘.
미정 정책은 임의로 확정하지 말고 선택지와 영향을 제시해줘.
```

장별 **고급 프롬프트 42개·후속 요청 21개·목적별 공통 프롬프트 4개·연계 시나리오 3개**는 [고급 활용법](docs/advanced-usage.md)에 있습니다.

---

## 간단한 사용법과 예시

아래는 **Codex 대화창 예시**입니다. Claude Code에서는 `$`를 `/`로, Antigravity에서는 앞부분을 “yuriring-game-system-design 스킬을 사용해”로 바꿉니다.

| 목적 | 짧은 요청 예시 | 기대 결과 |
|---|---|---|
| 개념 학습 | `$yuriring-game-system-design 기획 의도와 규칙의 관계를 새 예시로 설명하고 이해 확인 문제를 내줘.` | 개념·사례·이해 확인 |
| 자료 선택 | `$yuriring-game-system-design 다국어 NPC 대사 설계에 필요한 장과 참조 자료를 골라 핵심을 설명해줘.` | 관련 자료·책 근거·적용 기준 |
| 실무 설계 | `$yuriring-game-system-design 협동 RPG의 길드 기부를 설계해줘. 한도와 취소 정책은 미정이야.` | 의도·규칙·미정 정책 선택지 |
| 데이터 설계 | `$yuriring-game-system-design 7일 출석 이벤트의 메타·회차별 보상·아이템 참조를 설계해줘. 보상 수량은 미정이야.` | 행 의미·키 관계·중복 범위 |
| UI 명세 | `$yuriring-game-system-design 보유량 12인 아이템 수량 스테퍼의 상태와 오류를 명세해줘. 기본 수량은 미정이야.` | 입력·경계·버튼 상태 |
| 기획서 검토 | `$yuriring-game-system-design "./기획서.md"의 규칙·데이터·UI 모순을 문제→영향→수정안으로 검토해줘. 파일은 수정하지 마.` | 근거 위치·영향·수정 제안 |
| 대안 비교 | `$yuriring-game-system-design 출석 보상의 자동 지급과 직접 수령을 참여 흐름·피로·오류 복구 관점에서 비교해줘.` | 선택지·장단점·결정 질문 |
| 변경·QA | `$yuriring-game-system-design 방금 초안의 취소 허용안을 검토해줘. 기존 확정 정책은 유지하고 변경 영향과 QA 반례를 제시해줘.` | 영향 범위·회귀 시나리오 |

### 내 상황에 맞게 요청하기

짧은 질문으로 시작하고, 실제 설계에는 아래 항목을 필요한 만큼 덧붙입니다. 대괄호를 바꿔 입력하며, 같은 대화의 후속 요청에는 기존 결과를 이어서 사용합니다. **새 대화에서는 기존 문서나 답변도 함께 제공합니다.**

```text
$yuriring-game-system-design [기능 또는 문서]를 [설계/비교/검토]해줘.
맥락: [장르·대상·해결할 문제·기존 연결 기능]
확정: [유지해야 할 정책] / 미정: [아직 결정하지 않은 정책]
산출물: [의도문/규칙표/테이블/UI 명세/검토표] 중 필요한 것
관련 자료를 먼저 읽고 책의 근거와 새 제안을 구분해줘.
미정 사항은 선택지·영향·확인 질문으로 남겨줘.
```

데이터 설계에는 “**한 행의 의미·키·중복 금지 범위도 적어줘**”, QA에는 “**확정 정책의 필수 결과와 미정 정책별 조건부 결과를 나눠줘**”를 덧붙이면 검토 기준을 구체화할 수 있습니다. 적용 양식은 [실무 패턴](.agents/skills/yuriring-game-system-design/patterns.md)에 있습니다.

결과가 길면 “**현재 선택한 기능의 규칙표만 보완해줘**”처럼 범위를 좁힙니다. 더 깊은 요청은 [목적별 공통 프롬프트](docs/advanced-usage.md#목적별-공통-고급-프롬프트)와 [장별 예시](docs/advanced-usage.md#area-1)에서 고르세요.

---

## 스킬 구성과 작동 방식

**관련 장을 찾아 읽고 → 책의 개념을 적용하고 → 프로젝트의 정책과 가정을 구분해 → 설명·설계·검토 결과를 작성합니다.**

```text
.agents/skills/yuriring-game-system-design/
├── SKILL.md           # 핵심 원칙과 장별·주제별 색인
├── chapters/          # 전체 21장의 지식 파일
├── references/index.md # 구체적인 사례·질문별 참조 자료 선택
├── glossary.md        # 주요 용어와 관련 장
├── patterns.md        # 실무 적용 패턴
├── cheatsheet.md      # 상황별 판단표
├── source.json        # 출처·장 범위·검수 이력·한계
└── agents/openai.yaml # Codex용 표시·호출 메타데이터
```

Codex와 Antigravity는 `.agents/skills/yuriring-game-system-design/`을 사용하고, Claude Code는 `.claude/skills/yuriring-game-system-design`의 개별 상대 링크로 같은 원본을 읽습니다. [호스트별 사용](docs/agents.md)에서 연결과 인식 확인 방법을 볼 수 있습니다.

### 참조 자료 선택

> [!NOTE]
> **레퍼런스는 어디에 있나요?**
>
> `chapters/`와 용어·패턴·판단표가 실제 지식 자료이며, [참조 자료 색인](.agents/skills/yuriring-game-system-design/references/index.md)은 구체적인 테이블·UI·사례 이름을 해당 자료로 연결합니다. 장 번호를 몰라도 “다국어 대사”, “수량 스테퍼”, “자동 파견”처럼 요청하면 관련 장을 찾아 읽습니다. 책의 원문 전체나 모든 도판을 포함한 자료는 아닙니다.

색인에는 **사례·질문 26개, 여러 장을 연결하는 작업 6개, UX/UI 세부 원문 위치**가 있습니다. 개념의 의미는 용어집, 작성 양식은 실무 패턴, 초안의 빠른 검토는 판단표를 함께 사용합니다. [시작하기의 장 선택 예시](docs/getting-started.md#장-번호를-모를-때)를 참고하세요.

### 책의 전체 21장 구성

『유리링의 실전 게임 시스템 기획』은 **8부 21장**이며, 생성된 스킬의 `chapters/`에 **1장부터 21장까지 각각 한 파일**이 들어 있습니다. 각 파일은 개념·판단 기준·절차·안티패턴·사례·출처를 학습과 실무 적용에 맞게 합성한 자료입니다.

> [!NOTE]
> **읽는 방식**
>
> 필요한 장을 고르고 **개념 → 설계 예시 → 검토 예시 → 후속 요청**으로 활용하세요. 스킬은 원문 전체나 모든 그림·표의 복제본이 아닙니다.

아래 활용 영역은 탐색을 위한 편집 분류입니다. 장 제목은 [출처 기록](.agents/skills/yuriring-game-system-design/source.json)의 목차를 기준으로 표시했습니다. 장 번호 링크를 누르면 해당 스킬 파일을 읽을 수 있습니다.

| 활용 영역 | 장 | 책의 장 제목 | 주요 활용 |
|---|---|---|---|
| 역할과 관점 | [01](.agents/skills/yuriring-game-system-design/chapters/ch01-system-planning.md) | 시스템 기획이란 무엇인가? | 행동을 조건·판정·결과로 분해 |
| 역할과 관점 | [02](.agents/skills/yuriring-game-system-design/chapters/ch02-designer-perspective.md) | 시스템 기획자의 시야 | 화면 뒤의 구조와 의존 관계 분석 |
| 기획 의도 | [03](.agents/skills/yuriring-game-system-design/chapters/ch03-design-intent.md) | 기획 의도란 무엇인가? | 기능의 목적과 판단 기준 정리 |
| 기획 의도 | [04](.agents/skills/yuriring-game-system-design/chapters/ch04-intent-design.md) | 기획 의도 설계란? | 플레이 흐름·기대 효과·근거 설계 |
| 시스템 구조 | [05](.agents/skills/yuriring-game-system-design/chapters/ch05-system-decomposition.md) | 시스템 구조 분할 | 기능과 속성 분리 |
| 시스템 구조 | [06](.agents/skills/yuriring-game-system-design/chapters/ch06-system-composition.md) | 시스템의 구성 | 연동과 모듈 경계 정리 |
| 규칙과 예외 | [07](.agents/skills/yuriring-game-system-design/chapters/ch07-rule-components.md) | 규칙이란 무엇인가? | 조건·판정·예외 명세 |
| 규칙과 예외 | [08](.agents/skills/yuriring-game-system-design/chapters/ch08-rule-design.md) | 규칙의 설계 | 거래소 규칙과 충돌 검토 |
| 규칙과 예외 | [09](.agents/skills/yuriring-game-system-design/chapters/ch09-flowcharts.md) | 규칙의 도식화 | 정상·실패 분기 도식화 |
| 데이터 테이블 | [10](.agents/skills/yuriring-game-system-design/chapters/ch10-table-foundations.md) | 데이터 테이블이란 무엇인가? | 데이터 역할·타입 구분 |
| 데이터 테이블 | [11](.agents/skills/yuriring-game-system-design/chapters/ch11-table-design.md) | 데이터 테이블 설계 | 출석부·키·참조 설계 |
| 데이터 테이블 | [12](.agents/skills/yuriring-game-system-design/chapters/ch12-table-cases.md) | 데이터 테이블 사례 | 용도별 테이블·랜덤 풀 검토 |
| UX/UI | [13](.agents/skills/yuriring-game-system-design/chapters/ch13-ux-ui-foundations.md) | UX/UI란 무엇인가? | 사용자 경험과 인터페이스 연결 |
| UX/UI | [14](.agents/skills/yuriring-game-system-design/chapters/ch14-visual-interaction.md) | UX/UI 기획을 위한 구성 요소와 표현 기법 | 레이아웃·깊이·메타포 선택 |
| UX/UI | [15](.agents/skills/yuriring-game-system-design/chapters/ch15-ux-ui-design.md) | UX/UI 설계 | 행동 흐름·화면 영역·요소 명세 |
| UX/UI | [16](.agents/skills/yuriring-game-system-design/chapters/ch16-ui-components.md) | UX/UI 구성 요소 | 입력·알림·탐색 상태 설계 |
| 기획서 작성 | [17](.agents/skills/yuriring-game-system-design/chapters/ch17-document-purpose.md) | 시스템 기획서의 역할과 범위 | 독자·범위·전개 구성 |
| 기획서 작성 | [18](.agents/skills/yuriring-game-system-design/chapters/ch18-document-workshop.md) | 시스템 기획서 실습 | 캐릭터 파견 미션 명세 연결 |
| 기획서 작성 | [19](.agents/skills/yuriring-game-system-design/chapters/ch19-document-quality.md) | 시스템 기획서의 완성도를 결정하는 요소들 | 가독성·누락·AI 답변 검토 |
| 개발과 성장 | [20](.agents/skills/yuriring-game-system-design/chapters/ch20-production-lifecycle.md) | 디자인부터 런칭까지, 시스템 기획의 여정 | 개발·변경·라이브 운영 관리 |
| 개발과 성장 | [21](.agents/skills/yuriring-game-system-design/chapters/ch21-designer-growth.md) | 시스템 기획자 레벨 업 | 분석·협업·우선순위 훈련 |

`SKILL.md`의 장·주제 색인에서 필요한 장으로 이동하고, `glossary.md`로 용어를, `patterns.md`로 여러 장의 적용 패턴을, `cheatsheet.md`로 판단 기준을 확인합니다. 매 요청에서 21장을 모두 읽기보다 관련 장을 연결해 사용합니다. **[고급 활용법](docs/advanced-usage.md)**에서 각 장의 사용 시점·프롬프트·기대 산출물·연계 장과 전체 설계 시나리오를 확인하세요.

---

## 입력과 준비 상태

기획 아이디어·메모·기존 기획서와 **장르·대상·플레이 흐름·기술 제약·확정/미정 정책**을 제공합니다. 자료가 부족하면 무엇을 가정하고 무엇을 추가로 결정할지 구분해 달라고 요청하세요.

- **학습:** 장 번호나 알고 싶은 용어를 지정합니다.
- **설계:** 문제·목표 경험·기존 시스템과 원하는 산출물을 제공합니다.
- **검토:** 검토할 파일 경로 또는 본문과 유지해야 할 정책을 제공합니다.
- **원문 확인:** 해당 PDF·쪽을 제공하고 확인할 수치·표·도식을 좁힙니다.

스킬 이름이 보이지 않으면 현재 프로젝트, 원본 `SKILL.md`, Claude Code 링크를 확인하고 새 세션에서 다시 확인합니다. [문제 해결](docs/troubleshooting.md)을 참조하세요.

---

## 상세 가이드

| 필요한 내용 | 문서 |
|---|---|
| 전체 안내와 읽는 순서 | [가이드 홈](docs/index.md) |
| 준비·호출·결과 확인 | [시작하기](docs/getting-started.md) |
| 호스트별 호출·연결·앱 인식 | [에이전트별 사용](docs/agents.md) |
| 학습·설계·검토·반복 보완 흐름 | [기획 작업 흐름](docs/workflow.md) |
| 목적별 공통 프롬프트·21장별 전략·실무 시나리오 | [고급 활용법](docs/advanced-usage.md) |
| 원문·조건·수치·실제 답변 검수 | [검수와 테스트](docs/validation.md) |
| 인식·경로·내용 누락·원문 확인 문제 | [문제 해결](docs/troubleshooting.md) |

> [!TIP]
> **기본 사용에서 고급 활용까지**
>
> 시작하기에서 한 가지 요청을 실행한 뒤, 고급 활용법에서 해당 장의 예시를 고르고 후속 요청으로 보완하세요. 검수 가이드로 근거·가정·정책·반례를 확인합니다.

---

<a id="copyright"></a>

## ⚖️ Copyright & fair use

> [!WARNING]
> **책의 원문과 합성 지식 자료를 구분합니다**
>
> 이 스킬은 책의 내용을 학습·적용하도록 합성한 자료이며 원문 전체·추출 원문·도판을 복제한 자료가 아닙니다. 원본 자료와 책 기반 지식의 권리·공유 범위를 확인하고, 원문이나 전체 추출 텍스트를 공개 저장소에 포함하지 않습니다.

제공한 원문이나 기획서를 클라우드 에이전트가 읽으면 해당 서비스의 데이터 처리 조건이 적용됩니다. [공유와 배포](docs/validation.md#공유와-배포)를 참조하세요.

---

## 프로젝트 정보

- **프로젝트:** `yuriring-game-design`
- **실제 스킬 이름:** `yuriring-game-system-design`
- **참고 도서:** 정윤지(유리링), 『유리링의 실전 게임 시스템 기획』, 한빛미디어
- **구성:** 8부 21장, 사례·질문별 참조 색인과 용어·패턴·판단표
- **출처와 한계:** [source.json](.agents/skills/yuriring-game-system-design/source.json)
- **실행 지침:** [SKILL.md](.agents/skills/yuriring-game-system-design/SKILL.md)

사용자 문서는 루트 `README.md`와 `docs/`에 있습니다. 검수 기록은 기록된 검사 범위의 결과이며 원문의 모든 문장·도판이나 향후 모든 답변의 정확성을 보증하지 않습니다.
