# 자동 편성 결과 데이터 설계

## 설계 범위와 가정

- 자동 편성 요청 하나는 **확정된 결과 캐릭터 3명**을 가지며, 순위는 `1`, `2`, `3`을 각각 한 번씩 사용한다.
- 기존 캐릭터의 이름·등급·스탯 등 상세 정보는 캐릭터 데이터가 소유한다. 이 설계에는 `CharacterID`만 저장하고 상세 값을 복사하지 않는다.
- ID 생성 방식과 저장 기술은 미정이므로, 아래의 자료형은 논리 자료형이며 실제 DB·파일·서버 규약에 맞춰 매핑한다.

## 관계

```text
AutoFormationRequest 1 ── N AutoFormationSelection N ── 1 Character(기존 소유)
          요청 1건              요청의 순위별 선택 1건             캐릭터 정의 1건
```

`AutoFormationSelection`의 한 행은 “특정 자동 편성 요청에서 특정 순위로 확정된 캐릭터 한 명”이다. 요청 헤더와 반복되는 세 선택 행을 나누면, 요청별 결과와 캐릭터별 이력을 모두 같은 선택 행에서 조회할 수 있다.

## 데이터 표

### `AutoFormationRequest`

한 행은 자동 편성 요청 하나의 확정 결과 묶음이다.

| 칼럼 | 논리 자료형 | NULL | 키·규칙 | 의미·작동 규칙 |
|---|---|---:|---|---|
| `RequestID` | ID/STRING/INTEGER | 불가 | PK, 유일 | 요청 한 건을 식별한다. 생성 주체·형식·발번 시점은 저장 기술 결정 시 확정한다. |
| `FinalizedAt` | DATETIME | 불가 | 조회 정렬 기준 | 자동 편성 결과가 확정된 시각이다. 캐릭터별 선택 이력의 시간순 정렬에 사용한다. 시간대와 정밀도는 미정이다. |

### `AutoFormationSelection`

한 행은 요청 한 건의 순위 하나에 확정된 캐릭터 한 명이다.

| 칼럼 | 논리 자료형 | NULL | 키·규칙 | 의미·작동 규칙 |
|---|---|---:|---|---|
| `RequestID` | `AutoFormationRequest.RequestID`와 동일 | 불가 | PK 구성원, FK | 소속 요청을 참조한다. 요청 헤더가 존재해야 한다. |
| `Rank` | INTEGER | 불가 | PK 구성원, `CHECK (Rank IN (1, 2, 3))` | 요청 안의 편성 순위다. 낮을수록 상위 순위다. |
| `CharacterID` | 캐릭터 시스템의 ID와 동일 | 불가 | `UNIQUE (RequestID, CharacterID)`, 외부 참조 | 선택된 기존 캐릭터를 가리킨다. 이름·스탯은 저장하지 않는다. |

**키와 중복 금지 규칙**

```text
PRIMARY KEY (RequestID, Rank)
UNIQUE      (RequestID, CharacterID)
FOREIGN KEY (RequestID)  → AutoFormationRequest(RequestID)
CharacterID             → Character의 기존 기본 키(소유 시스템에서 존재 검증)
```

- `(RequestID, Rank)` 기본 키가 같은 요청의 같은 순위를 한 번만 보관하게 한다.
- `(RequestID, CharacterID)` 유일성 규칙이 같은 요청에서 같은 캐릭터가 두 순위를 차지하는 일을 막는다.
- 서로 다른 `RequestID`에는 이 두 조합이 다시 나타날 수 있으므로, 같은 캐릭터를 다른 요청에서 재선택할 수 있다.

`Rank`의 범위와 두 유일성 제약만으로는 요청마다 결과 행이 정확히 세 개인지 보장할 수 없다. 결과 확정 처리에서 세 행을 하나의 단위로 검증·저장해야 한다.

## 확정 처리와 검증 예시

저장 기술에 독립적인 처리 순서의 예시다.

```text
입력: [(1, C101), (2, C205), (3, C318)]

1. 선택 수가 정확히 3개인지 확인한다.
2. Rank 집합이 정확히 {1, 2, 3}인지 확인한다.
3. CharacterID 3개가 모두 다른지 확인한다.
4. 각 CharacterID가 기존 캐릭터 데이터에 존재하는지 확인한다.
5. 하나의 원자적 저장 단위에서 Request 행과 Selection 3행을 저장한다.
6. 어느 검증 또는 저장이 실패하면 전체를 확정하지 않는다.
```

관계형 저장소라면 1~4단계와 행 삽입을 트랜잭션으로 묶고, 위의 PK·UNIQUE·CHECK 제약을 함께 둔다. 다른 저장 방식에서도 같은 원자성, 두 중복 금지 범위, 참조 존재 검증을 구현 계약으로 유지한다.

| 검증 입력 | 기대 결과 |
|---|---|
| `[(1,C101),(2,C205),(3,C318)]`, 세 캐릭터가 존재 | 요청 1건과 선택 3건이 확정된다. |
| `[(1,C101),(2,C101),(3,C318)]` | 같은 요청의 `CharacterID` 중복으로 거부된다. |
| `[(1,C101),(1,C205),(3,C318)]` | 같은 요청의 `Rank` 중복으로 거부된다. |
| `[(1,C101),(2,C205)]` | 결과가 3명이 아니므로 거부된다. |
| `[(1,C101),(2,C205),(4,C318)]` | 허용 순위 밖이므로 거부된다. |
| 다른 요청에서 다시 `C101` 선택 | 허용된다. |

## 조회 예시

아래는 SQL과 유사한 의사 질의다. 실제 문법은 저장 기술에 맞춘다.

### 요청별 편성 결과

```sql
SELECT r.RequestID, r.FinalizedAt, s.Rank, s.CharacterID
FROM AutoFormationRequest AS r
JOIN AutoFormationSelection AS s ON s.RequestID = r.RequestID
WHERE r.RequestID = :requestId
ORDER BY s.Rank ASC;
```

결과의 `CharacterID` 목록으로 캐릭터 소유 시스템을 조회해 현재의 이름·표시 정보를 결합한다. 이 테이블에는 캐릭터 상세를 복제하지 않는다.

### 캐릭터별 선택 이력

```sql
SELECT s.CharacterID, s.Rank, r.RequestID, r.FinalizedAt
FROM AutoFormationSelection AS s
JOIN AutoFormationRequest AS r ON r.RequestID = s.RequestID
WHERE s.CharacterID = :characterId
ORDER BY r.FinalizedAt DESC, r.RequestID DESC;
```

성능 검토 시 이력 조회가 빈번하면 `AutoFormationSelection(CharacterID, RequestID)` 인덱스를 후보로 둔다. 인덱스 지원 여부와 실제 조회량은 저장 기술을 정한 뒤 확인한다.

## 책에서 참고한 원칙

- 한 행이 무엇을 뜻하는지 먼저 고정하고, 반복되는 정보를 별도 행으로 분리해 키로 연결한다. 여기서는 요청 하나와 요청의 순위별 선택 하나가 서로 다른 객체다. (10장·11장)
- 다른 시스템이 소유한 상세 정보는 복사하지 않고 키로 참조하며, 참조 대상의 존재를 검증한다. 따라서 캐릭터 정보 대신 `CharacterID`만 보관한다. (10장·11장)
- 기본 키의 고유성만으로 업무상 중복 금지 범위를 대신하지 않는다. 요청 안의 캐릭터 중복과 순위 중복은 각각 별도 유일성 규칙으로 표현한다. (11장 및 실무 패턴의 객체·반복 정보·참조 분리)
- 자료형뿐 아니라 값 범위와 실제 작동 규칙을 적고, 가데이터와 경계 사례로 관계를 검증한다. (10장·11장)

## 가상 프로젝트에 적용한 제안

- 요청 헤더 `AutoFormationRequest`와 반복 선택 행 `AutoFormationSelection`의 1:N 구조.
- `PRIMARY KEY (RequestID, Rank)` 및 `UNIQUE (RequestID, CharacterID)`로 확정 정책을 구현 가능한 제약으로 옮긴 방식.
- `FinalizedAt`을 선택 이력 정렬 기준으로 둔 방식과 `CharacterID` 이력 인덱스 후보.
- 세 결과를 정확히 한 묶음으로 확정하기 위한 원자적 저장 절차.

## 미정 사항과 개발 결정 질문

| 미정 사항 | 결정이 필요한 이유 |
|---|---|
| `RequestID` 생성 규칙 | 숫자·UUID·문자열 키, 생성 주체, 충돌 방지 방식이 저장 기술과 운영 도구에 따라 달라진다. |
| 결과 확정 시점·시간대 | `FinalizedAt`의 기록 시점과 이력 정렬의 동률 처리 규칙을 정해야 한다. |
| 재시도·중복 API 요청 | 다른 `RequestID`로 같은 요청이 재전송되는 것을 같은 요청으로 볼지 정해야 한다. 막아야 한다면 요청 멱등성 키와 유일성 범위를 추가한다. |
| 캐릭터 삭제·비활성화·표시명 변경 | 이력에서 현재 캐릭터 정보를 보여 줄지, 확정 당시 표시값을 별도로 남길지 정책이 필요하다. |
| 캐릭터 선택 가능 조건 | 캐릭터 존재 외에 잠금·사용 중·소유자 조건을 검증할지, 그 책임이 어느 시스템에 있는지 정해야 한다. |
