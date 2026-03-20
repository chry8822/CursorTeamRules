---
name: cursor-commit
description: Git 커밋 자동화. git diff를 분석해 커밋 타입 선택 → 메시지 초안 생성 → 확인 후 커밋 실행 → push 여부 확인 순서로 진행한다. 사용자가 커밋, commit, /commit 을 요청할 때 사용한다.
---

# Commit

프로젝트에 `.cursor/commands/commit.md` 가 있으면 반드시 그 파일을 읽고 절차대로 실행한다.

없을 경우 아래 절차를 따른다.

## 규칙

- push는 반드시 사용자 확인 후에만 실행한다
- 변경 파일이 없으면 "변경된 파일이 없습니다"를 출력하고 종료한다
- PowerShell 환경이므로 `&&` 대신 `;` 또는 임시 파일 방식 사용

## STEP 0. 제목 인자 확인

`/commit 제목 텍스트` 처럼 텍스트가 있으면 STEP 4로 바로 이동한다.
없으면 STEP 1부터 순서대로 진행한다.

## STEP 1. 변경 파일 파악

```
git status
git diff HEAD
```

## STEP 2. 커밋 타입 선택

변경 내용 요약 후 아래 타입 선택지를 출력한다.

```
1. feat / 2. fix / 3. refactor / 4. style / 5. chore / 6. docs / 7. test / 8. 직접 입력
```

## STEP 3. 메시지 초안 생성

diff 전체를 기반으로 아래 형식으로 출력한다.

```
──────────────────────────────────
생성된 커밋 메시지:
  타입: 제목

변경 요약:
- 파일명: 변경 내용

이 메시지로 커밋할까요? (y / n / 직접 입력)
──────────────────────────────────
```

## STEP 4. 커밋 실행 (PowerShell)

```powershell
Set-Content -Path "$env:TEMP\COMMIT_MSG_TEMP.txt" -Value "제목`n`n변경 요약"
git add .
git commit -F "$env:TEMP\COMMIT_MSG_TEMP.txt"
```

커밋 완료 후 push 여부를 묻는다.

## STEP 5. Push (선택)

사용자가 y 입력 시 `git push` 실행 후 종료한다.
