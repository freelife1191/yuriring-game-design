# 릴리스 노트와 GitHub Release

[README](../README.md) · [문서 가이드](../docs/index.md) · [현재 릴리스 노트](2026-10-15-documentation-and-release-guide.md) · [노트 템플릿](_template.md)

`release/`는 GitHub Release에 넣을 **검토 가능한 노트 원본**을 보관하는 경로입니다. 이 폴더에 파일을 커밋하는 일만으로는 공개 GitHub Release가 만들어지지 않습니다.

> [!IMPORTANT]
> **날짜, 버전, 태그는 서로 다른 값입니다.**
>
> 릴리스 노트 파일명은 작성일(KST)을 기록하고, Git 태그와 GitHub Release 제목은 실제 배포에서 별도로 결정합니다. 노트 파일의 날짜가 버전 번호나 태그 이름을 대신하지 않습니다.

## 목차

- [파일명과 내용 규칙](#파일명과-내용-규칙)
- [새 태그로 초안 Release 만들기](#새-태그로-초안-release-만들기)
- [기존 태그에 Release 만들기](#기존-태그에-release-만들기)
- [검토 후 공개하기](#검토-후-공개하기)
- [게시 전 점검](#게시-전-점검)

---

## 파일명과 내용 규칙

| 항목 | 규칙 | 예시 |
|---|---|---|
| 노트 경로 | `release/yyyy-mm-dd-title.md` | `release/2026-10-15-documentation-and-release-guide.md` |
| 날짜 | 한국 표준시(KST) 기준 작성일 | `2026-10-15` |
| 제목 부분 | 영문 소문자·숫자·하이픈만 사용하는 lower kebab-case | `documentation-and-release-guide` |
| 버전과 태그 | 파일명과 별도 관리 | `v0.2.0` |
| Release 제목 | 사람이 읽는 제목 | `v0.2.0 — Documentation and release guide` |

새 노트는 [_template.md](_template.md)를 복사해 시작합니다. 노트에는 실제 변경 범위, 검증 결과, 남은 한계를 적습니다. 실행하지 않은 호스트 호출이나 전수 검증을 수행했다고 쓰지 않습니다.

<details>
<summary>새 릴리스 노트의 최소 골격 보기</summary>

```markdown
# Release: 제목

- 작성일(KST): YYYY-MM-DD
- 버전/태그: 배포 시 결정

## 변경 사항

## 검증

## 한계와 후속 확인
```

</details>

---

## 새 태그로 초안 Release 만들기

새 태그가 아직 없을 때만 `--target`에 **완전한 커밋 SHA**를 지정합니다. 먼저 커밋을 확정하고 SHA를 확인한 뒤, 초안으로 올립니다.

```bash
git rev-parse HEAD
gh release create TAG \
  --repo freelife1191/yuriring-game-design \
  --target FULL_COMMIT_SHA \
  --title "TITLE" \
  --notes-file release/FILE \
  --draft
```

예를 들어 `TAG`는 `v0.2.0`, `TITLE`은 `v0.2.0 — Documentation and release guide`, `FILE`은 `2026-10-15-documentation-and-release-guide.md`처럼 실제 값으로 바꿉니다. `--draft`는 검토 전 공개를 막기 위한 기본값입니다.

`gh release create`의 `--target`은 태그가 없을 때 생성할 태그의 기준 커밋을 지정합니다. 자세한 동작은 [GitHub CLI `gh release create` 공식 문서](https://cli.github.com/manual/gh_release_create)를 확인하세요.

---

## 기존 태그에 Release 만들기

이미 만든 태그를 Release로 게시할 때는 `--verify-tag`를 사용합니다. 이 경로에서는 태그 생성 기준을 지정하는 `--target`을 함께 쓰지 않습니다.

```bash
gh release create TAG \
  --repo freelife1191/yuriring-game-design \
  --verify-tag \
  --title "TITLE" \
  --notes-file release/FILE \
  --draft
```

> [!WARNING]
> **태그의 커밋을 확인하세요.** `--verify-tag`는 태그가 이미 원격에 존재하는지 확인합니다. 의도한 커밋을 가리키는지는 릴리스 전에 별도로 점검해야 합니다.

---

## 검토 후 공개하기

초안의 태그·제목·노트와 연결 커밋을 검토한 뒤에만 공개합니다.

```bash
gh release edit TAG \
  --repo freelife1191/yuriring-game-design \
  --draft=false
```

공개 전환과 수정 옵션은 [GitHub CLI `gh release edit` 공식 문서](https://cli.github.com/manual/gh_release_edit)를 따릅니다.

---

## 게시 전 점검

- [ ] 노트 파일명이 `yyyy-mm-dd-title.md`이고 날짜는 KST 작성일인가?
- [ ] 노트의 범위가 실제 커밋과 일치하는가?
- [ ] 새 태그는 `FULL_COMMIT_SHA`에, 기존 태그는 의도한 원격 태그에 연결되는가?
- [ ] `--notes-file`이 대상 노트를 가리키는가?
- [ ] 초안에서 제목·노트·대상 커밋을 검토했는가?
- [ ] 공개 전환은 `gh release edit TAG --draft=false`로 별도로 실행하는가?

현재 문서 변경의 노트는 [2026-10-15-documentation-and-release-guide.md](2026-10-15-documentation-and-release-guide.md)에 있습니다.

## 기존 Release에 노트 적용하기

현재 본문과 새 노트를 비교한 뒤 적용합니다. 이 명령은 본문 전체를 교체하며 공개 상태를 바꾸지 않습니다.

```bash
gh release edit TAG \
  --repo freelife1191/yuriring-game-design \
  --notes-file release/FILE
```

> [!TIP]
> GitHub Release 화면에서 연결할 문서는 전체 GitHub URL로 적습니다. 특정 배포본에 고정할 링크는 해당 태그나 커밋 URL을 사용하세요. 노트와 배포 파일에는 인증 값·원문 PDF·로컬 실행 로그를 포함하지 않습니다.
