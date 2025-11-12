# Split Commit - 커밋 분리

하나의 큰 변경사항을 논리적으로 여러 개의 작은 커밋으로 분리합니다.

## 사용법

```bash
/split-commit [옵션]
```

## 옵션

- `--interactive`: 대화형 모드로 파일별로 선택
- `--by-type`: Type별로 자동 분리
- `--by-scope`: Scope별로 자동 분리
- `--by-file`: 파일별로 분리

## 실행 내용

1. **변경사항 분석**
   ```bash
   git status
   git diff --stat
   ```

2. **분리 전략 제안**
   - Type별 분류
   - Scope별 분류
   - 파일별 분류
   - 논리적 단위별 분류

3. **각 커밋 생성**
   - 파일별로 스테이징
   - 적절한 커밋 메시지 생성
   - 순차적으로 커밋 실행

## 예시

### 현재 변경사항

```
변경된 파일:
  M src/auth/login.ts        (기능 추가)
  M src/auth/token.ts        (기능 추가)
  M src/api/user.ts          (버그 수정)
  M README.md                (문서 수정)
  M src/utils/format.ts      (리팩토링)
```

### 분리 제안

```
💡 5개의 파일을 3개의 커밋으로 분리하는 것을 권장합니다:

Commit 1 [feat]: 인증 기능 추가
  - src/auth/login.ts
  - src/auth/token.ts

Commit 2 [fix]: 사용자 API 버그 수정
  - src/api/user.ts

Commit 3 [refactor]: 유틸리티 함수 리팩토링
  - src/utils/format.ts

Commit 4 [docs]: README 업데이트
  - README.md

이 순서로 진행하시겠습니까? (y/n/edit)
```

## 대화형 모드

```bash
/split-commit --interactive
```

각 파일을 하나씩 검토하면서 어떤 커밋에 포함할지 선택합니다.

```
파일: src/auth/login.ts
변경 내용: [diff 표시]

이 파일을 어떻게 처리하시겠습니까?
1. 새 커밋 "feat(auth): Add login feature"에 포함
2. 기존 커밋에 추가
3. 스킵
선택:
```

## Type별 자동 분리

```bash
/split-commit --by-type
```

변경사항을 자동으로 Type(feat/fix/refactor 등)별로 분류하여 커밋합니다.

## 관련 명령어

- `/auto-commit` - 자동 커밋 메시지 생성
- `/commit-history` - 커밋 히스토리 분석

