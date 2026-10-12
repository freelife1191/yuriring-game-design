# 자동 편성 결과 데이터 설계

## 범위와 관계

- 요청 한 건은 확정 결과 캐릭터 3명을 가지며, 순위 `1`·`2`·`3`을 각각 한 번씩 사용한다.
- 캐릭터의 이름·등급·스탯은 기존 캐릭터 데이터의 책임이다. 이 설계에는 `CharacterID`만 보관한다.
- ID 생성 방식과 저장 기술은 미정이므로 자료형·키는 논리 명세다.

```text
AutoFormationRequest 1 ── N AutoFormationSelection N ── 1 Character(기존 소유)
        요청 1건              요청 안의 순위별 결과 1건          캐릭터 정의 1건
```

`AutoFormationSelection`의 한 행은 “특정 요청에서 특정 순위로 확정된 캐릭터 한 명”이다. 요청 메타와 반복되는 세 선택 결과를 분리해, 요청별 결과와 캐릭터별 선택 이력을 같은 선택 행에서 조회한다.

## 데이터 표

### `AutoFormationRequest`

한 행은 자동 편성 요청 하나의 확정 결과 묶음이다.

| 칼럼 | 논리 자료형 | NULL | 키·규칙 | 의미·작동 규칙 |
|---|---|---:|---|---|
| `RequestID` | ID | 불가 | PK, 유일 | 같은 요청을 식별한다. ID 형식·발번 주체·생성 시점은 미정이다. |
| `FinalizedAt` | DATETIME | 불가 | 이력 정렬 기준 | 결과 확정 시각이다. 시간대와 정밀도는 기술 결정 시 확정한다. |

### `AutoFormationSelection`

한 행은 요청 한 건의 순위 하나에 확정된 캐릭터 한 명이다.

| 칼럼 | 논리 자료형 | NULL | 키·규칙 | 의미·작동 규칙 |
|---|---|---:|---|---|
| `RequestID` | `AutoFormationRequest.RequestID`와 동일 | 불가 | PK 구성원, 요청 참조 | 소속 요청이다. 요청 헤더가 존재해야 한다. |
| `Rank` | INTEGER | 불가 | PK 구성원, `CHECK (Rank IN (1, 2, 3))` | 요청 안의 편성 순위다. 숫자가 작을수록 상위다. |
| `CharacterID` | 캐릭터 소유 데이터의 ID와 동일 | 불가 | `UNIQUE (RequestID, CharacterID)`, 캐릭터 참조 | 확정된 기존 캐릭터다. 상세 정보는 저장하지 않는다. |

### 식별·중복 금지 계약

```text
PRIMARY KEY (RequestID, Rank)
UNIQUE      (RequestID, CharacterID)
RequestID   → AutoFormationRequest.RequestID
CharacterID → 기존 Character의 식별자
```

- `(RequestID, Rank)`는 같은 요청의 같은 순위를 한 번만 저장하게 한다.
- `(RequestID, CharacterID)`는 같은 요청에 같은 캐릭터가 두 순위를 차지하는 일을 막는다.
- 두 제약은 `RequestID` 범위 안에서만 적용된다. 다른 요청에서 같은 `CharacterID`를 선택하는 것은 허용한다.
- `CharacterID`가 같은 저장소에 있으면 외래 키로, 별도 서비스에 있으면 확정 전에 해당 서비스의 존재 검증으로 구현한다. 어느 경우든 캐릭터 상세 정보는 복제하지 않는다.

`Rank` 범위와 두 유일성 제약은 중복을 막지만 결과 행이 정확히 세 개인지는 보장하지 않는다. 세 행을 하나의 원자적 확정 단위로 처리해야 한다.

## 확정·검증 예시

아래는 저장 기술에 독립적인 처리 계약이다.

```text
입력: [(1, C101), (2, C205), (3, C318)]

1. 선택 수가 정확히 3개인지 확인한다.
2. Rank 집합이 정확히 {1, 2, 3}인지 확인한다.
3. CharacterID 3개가 모두 다른지 확인한다.
4. 각 CharacterID가 캐릭터 소유 데이터에 존재하는지 확인한다.
5. Request 행과 Selection 3행을 하나의 원자적 단위로 저장한다.
6. 어느 단계든 실패하면 요청 결과를 확정하지 않는다.
```

관계형 저장소라면 위 검증과 삽입을 하나의 트랜잭션으로 묶고 PK·UNIQUE·CHECK 제약을 둔다. 다른 저장 방식도 같은 검증과 원자적 저장을 보장해야 한다.

| 사전 조건·입력 | 확정 정책의 필수 결과 |
|---|---|
| 세 캐릭터가 존재, `[(1,C101),(2,C205),(3,C318)]` | 요청 1건과 선택 3건이 함께 확정된다. |
| `[(1,C101),(2,C101),(3,C318)]` | 같은 요청의 캐릭터 중복이므로 확정되지 않는다. |
| `[(1,C101),(1,C205),(3,C318)]` | 같은 요청의 순위 중복이므로 확정되지 않는다. |
| `[(1,C101),(2,C205)]` | 3명을 만족하지 않으므로 확정되지 않는다. |
| `[(1,C101),(2,C205),(4,C318)]` | 허용 순위 밖이므로 확정되지 않는다. |
| 새 `RequestID`에서 `C101` 재선택 | 서로 다른 요청이므로 허용된다. |

동일 요청의 재전송에 기존 결과를 반환할지 오류로 처리할지는 미정이다. 어느 응답 방식을 택해도 이미 확정된 요청에 선택 행을 추가 반영해서는 안 된다. 다른 `RequestID`로 전달된 재시도가 같은 업무 요청인지 판별하려면 별도의 멱등성 키 정책이 필요하다.

## 조회 예시

아래 SQL 유사 문법은 논리적 조회를 보인 것이다.

### 요청별 결과

```sql
SELECT r.RequestID, r.FinalizedAt, s.Rank, s.CharacterID
FROM AutoFormationRequest AS r
JOIN AutoFormationSelection AS s ON s.RequestID = r.RequestID
WHERE r.RequestID = :requestId
ORDER BY s.Rank ASC;
```

반환된 `CharacterID`들로 캐릭터 소유 데이터를 조회해 현재 표시 정보를 결합한다.

### 캐릭터별 선택 이력

```sql
SELECT s.CharacterID, s.Rank, r.RequestID, r.FinalizedAt
FROM AutoFormationSelection AS s
JOIN AutoFormationRequest AS r ON r.RequestID = s.RequestID
WHERE s.CharacterID = :characterId
ORDER BY r.FinalizedAt DESC, r.RequestID DESC;
```

이력 조회가 빈번한 저장소라면 `AutoFormationSelection(CharacterID, RequestID)` 인덱스를 후보로 검토한다. 필요성·문법·지원 여부는 실제 저장 기술과 조회량을 확인한 뒤 결정한다.

## 책에서 직접 참고한 원칙

- 테이블의 한 행이 의미하는 객체를 먼저 정하고, 반복되는 정보를 별도 행으로 분리해 키로 연결한다. 여기서는 요청과 요청의 순위별 선택이 서로 다른 객체다. (10장, 책 p.164–169; 11장, 책 p.176–183)
- 다른 시스템이 소유한 상세 정보는 중복 입력하지 않고 식별자로 참조하며, 참조 대상의 존재를 확인한다. 따라서 캐릭터 상세 대신 `CharacterID`만 저장한다. (10장, 책 p.167–172; 11장, 책 p.176–183)
- 키·자료형뿐 아니라 값 범위와 실제 작동 규칙을 명세하고, 가데이터와 경계 사례로 구조를 검증한다. (10장, 책 p.164–172; 11장, 책 p.184–190)
- 사례 테이블 구조는 프로젝트의 저장 구조·데이터 흐름·협업 방식에 따라 달라질 수 있으므로 범용 정답으로 복제하지 않는다. (12장, 책 p.192–218)

## 스킬의 적용 지침과 가상 프로젝트 제안

아래는 책의 직접 원문 스키마가 아니다.

- **스킬의 적용 지침:** 기본 키의 고유성만으로 요청 안의 업무상 중복 금지를 충족한다고 가정하지 않는다. 자동 편성 사례에는 `RequestID + CharacterID`와 `RequestID + Rank`를 별도로 검증한다. 이는 검증 기록을 반영한 보완 지침이다.
- **가상 프로젝트 제안:** `AutoFormationRequest`/`AutoFormationSelection`의 1:N 구조, `(RequestID, Rank)` 복합 기본 키, `(RequestID, CharacterID)` 유일성 제약, `FinalizedAt`, 세 행의 원자적 확정 절차 및 이력 인덱스 후보를 제안한다.

## 개발 결정 질문

| 미정 사항 | 결정이 필요한 이유 |
|---|---|
| `RequestID` 발번 규칙 | 숫자·문자열·UUID, 생성 주체와 충돌 방지 방식이 저장 기술에 따라 달라진다. |
| 재전송·멱등성 | 다른 `RequestID`로 재시도된 요청을 같은 업무 요청으로 막을지 정해야 한다. |
| 캐릭터의 삭제·비활성화·표시명 변경 | 이력에서 현재 캐릭터 정보를 보여 줄지, 확정 당시 표시값을 별도 보존할지 결정해야 한다. |
| 선택 가능 조건 | 존재 여부 외에 소유자·잠금·사용 중 상태를 확인할지와 책임 시스템을 정해야 한다. |
| 시각 규약 | `FinalizedAt`의 시간대·정밀도·동률 정렬 규칙을 정해야 한다. |
