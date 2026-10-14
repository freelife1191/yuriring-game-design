# Release: Documentation and release guide

- 작성일(KST): 2026-10-15

> [!NOTE]
> 이 노트는 2026-10-15의 문서 구조 변경 범위만 기록합니다. 이 파일을 커밋해도 GitHub Release가 공개되지는 않습니다. 게시 절차는 [릴리스 가이드](https://github.com/freelife1191/yuriring-game-design/blob/main/release/README.md)를 따릅니다.

## 변경 사항

### 추가

- 루트 README 상단에 정적 SVG 배너, 프로젝트 소개, GitHub 저장소·Release 배지, 주요 문서 바로가기를 추가했습니다.
- `docs/assets/banner.svg`에 외부 파일이나 런타임에 의존하지 않는 프로젝트 배너를 추가했습니다.
- `release/`에 릴리스 노트 파일명 규칙, 작성 템플릿, 초안 검토와 공개 절차를 추가했습니다.

### 변경

- README와 사용자 문서에 가이드 홈·README·릴리스 경로를 연결하고, 읽기 순서가 긴 문서에는 목차를 추가했습니다.
- 문서 가이드와 README에서 현재 릴리스 노트 및 템플릿으로 이동할 수 있게 했습니다.

## 검증

- `git diff --check`와 신규 파일의 개별 공백 검사 통과.
- 사용자 문서 Markdown 11개의 로컬 링크·앵커 정적 점검 통과.
- `docs/assets/banner.svg`의 XML 형식 점검 통과.

## 한계와 후속 확인

- 이번 변경은 문서·정적 SVG·릴리스 노트 구조만 다룹니다. `.agents/skills/`의 지식 파일과 스킬 동작은 수정하지 않았습니다.
- 호스트 앱에서의 실제 스킬 호출은 이번 문서 변경의 검증 범위에 포함하지 않습니다.
