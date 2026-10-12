# 요청 1 — 다국어 NPC 대사 데이터 검토

## 결론

UI 문구와 NPC 대사를 같은 표에 한글 원문으로 직접 저장하는 방식은 번역 수정과 재사용의 관리 지점을 늘린다. 이 스킬의 12장 구조를 따르면 **텍스트는 스트링 테이블**, **대사의 재생 구조는 다이얼로그 챕터와 다이얼로그 행**이 맡는 편이 맞다. UI 문구와 대사는 모두 스트링 키를 참조하되, 대사 행에는 챕터 소속·재생 순서·화자·표정·음성 리소스를 둔다.

근거: [ch12](/Users/freelife/ai/yuriring-game-design/.agents/skills/yuriring-game-system-design/chapters/ch12-table-cases.md)의 텍스트 사례(책 p.194–199 / PDF p.196–201)는 “문자열은 키+언어별 값”, “다이얼로그는 챕터 ID가 대사 행을 묶고, 대사 행은 스트링 키·캐릭터·표정·음성으로 연결”한다고 설명한다. [ch11](/Users/freelife/ai/yuriring-game-design/.agents/skills/yuriring-game-system-design/chapters/ch11-table-design.md)은 다른 시스템이 소유한 상세 정보는 복사하지 않고 키로 참조하고, 반복 구조는 별도 행으로 분리한다고 정리한다(책 p.176–188 / PDF p.178–190).

## 권장 책임과 연결

| 책임 | 행의 의미 | 필요한 연결 | 검토할 규칙 |
| --- | --- | --- | --- |
| 스트링 | 하나의 텍스트 키에 대한 언어별 값 | `StringKey`를 UI와 대사가 참조 | 지원 언어, 누락 번역 시 표시 정책, 긴 문자열 처리 |
| 다이얼로그 챕터 | 대사 묶음 하나 | 챕터 키를 대사 행이 참조 | 재생 범위·진입 조건·반복 재생 정책 |
| 다이얼로그 | 한 번 재생할 대사/연출 단위 | 챕터 키, 스트링 키, 화자·표정·음성 리소스 참조 | 재생 순서, 스킵·반복·중단 뒤 재개 정책 |
| UI 문구 사용처 | 화면 요소 하나가 요구하는 문구 | 스트링 키만 참조 | 문구 갱신 시점과 긴 번역문 레이아웃 처리 |

`StringKey` 등 표의 이름은 설명을 위한 예시다. 실제 칼럼명·키 형식은 프로젝트 규약으로 정해야 한다. 한 행이 무엇을 뜻하는지, 외래 키의 대상·존재 검증, 언어별 값의 완결성은 [ch10](/Users/freelife/ai/yuriring-game-design/.agents/skills/yuriring-game-system-design/chapters/ch10-table-foundations.md)의 기본 키·외래 키 원칙(책 p.164–169 / PDF p.166–171)에 따라 명세한다.

## 반복 연출과 다국어 확장 점검

1. 대사 순서는 텍스트 자체가 아니라 대사 행의 재생 순서로 관리한다. 같은 문장을 다른 챕터에서 재사용할 수 있고, 번역은 스트링 테이블 한 곳에서 수정된다.
2. 화자·표정·음성은 텍스트 값에 섞지 않고 대사 행에서 각각 참조한다. 반복 연출이 늘어날 때 `대사1/대사2` 같은 열 증식보다 대사 행을 늘리는 구조가 낫다.
3. UI 문구도 한글 문자열을 각 화면 표에 복사하지 않고 같은 스트링 키 체계를 사용한다. UI에서는 언어별 길이 초과 처리와 갱신 정책도 함께 정한다([ch15](/Users/freelife/ai/yuriring-game-design/.agents/skills/yuriring-game-system-design/chapters/ch15-ux-ui-design.md), PDF p.264–265).
4. 가데이터로 언어 2개 이상, 빈 번역, 긴 번역, 같은 문자열의 UI/대사 재사용, 챕터 내 반복 재생을 확인한다([ch11](/Users/freelife/ai/yuriring-game-design/.agents/skills/yuriring-game-system-design/chapters/ch11-table-design.md), 책 p.188–190 / PDF p.190–192).

## 정확한 원문 칼럼명 복원 가능 여부

불가능하다. 현재 읽은 스킬 자료의 한계는 **역할과 연결 관계**를 제공하지만, 원문 다이얼로그 표의 전체 칼럼명·순서·자료형 목록을 전부 전사하지 않는다는 점이다. 확인되는 것은 `챕터 ID`, `스트링 키`, `캐릭터`, `표정`, `음성 리소스`라는 역할이며, 이것이 원문의 정확한 영문 헤더인지까지는 이 자료만으로 단정할 수 없다. 원문 표의 정확한 칼럼명을 확정하려면 책 PDF의 해당 표를 별도로 확인해야 한다.

## 실제 읽은 파일

- `SKILL.md`
- `chapters/ch10-table-foundations.md`
- `chapters/ch11-table-design.md`
- `chapters/ch12-table-cases.md`
- `chapters/ch15-ux-ui-design.md`
- `references/index.md`
