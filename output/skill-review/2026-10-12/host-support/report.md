# Claude Code·Antigravity 지원 및 문서 위치 검증

프로젝트 지원 파일 구성을 완료했다. README의 물리 원본은 프로젝트 루트, 사용자 가이드 7개의 물리 원본은 루트 docs에 있다. 이전 README·한국어 가이드 경로는 호환 링크로 연결한다.

Claude Code에는 변환기와 생성된 게임 기획 스킬의 개별 링크 2개를 추가하고 대상·SKILL.md 읽기를 확인했다. 링크 경로로 추출기 --check도 실행했다. Antigravity 2.0의 공식 프로젝트 경로인 .agents/skills를 직접 사용한다. 추가 복제는 만들지 않았다. 실제 앱 메뉴에서 스킬 이름을 관찰한 검증은 별도로 남는다.

- 전체 테스트: 904 통과, 8 건너뜀. 최종 범위 선택·Antigravity 테스트 14개 재확인.
- 린트: 통과. skill-creator 형식 검사: 통과.
- Claude Code 렌즈 검사: 오류 없음, 기존 본문 500줄 권고 초과 경고 1개.
- Antigravity 경로 탐색: 변경 전 1/5 → 변경 후 5/5. 실제 SKILL.md의 셸 블록을 격리 파일 구조에서 실행했다.
- 주 검수의 직접 링크 70개 손상 없음. 독립 검수는 생성 스킬까지 포함해 93개 확인, 손상 없음.
- 생성된 책 스킬의 모든 파일 해시는 변경되지 않았다.

[상세 증거](report.json) · [수정 내역](changes.patch) · [루트 README](../../../../README.md) · [호스트 가이드](../../../../docs/agents.md)

[Claude Code 공식 문서](https://code.claude.com/docs/en/skills)와 [Antigravity 공식 문서](https://antigravity.google/docs/skills)를 2026-10-12 기준으로 확인했다. 로컬 버전 확인은 Claude Code CLI 2.1.296, Antigravity 앱 메타데이터 2.0.1이며, 앱에서 스킬을 실제 실행했다는 결과로 취급하지 않는다.
