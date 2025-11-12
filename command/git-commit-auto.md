# Auto Commit - 자동 커밋 메시지 생성

자동으로 변경사항을 분석하여 적절한 커밋 메시지를 생성하고 커밋합니다.

## 사용법

```bash
/auto-commit [options]
```

## 옵션

- `--dry-run`: 커밋하지 않고 메시지만 미리보기
- `--stage-all`: 모든 변경사항 자동으로 스테이징
- `--push`: 커밋 후 자동으로 push
- `--no-body`: Body 없이 Subject만 생성

## 실행 순서

1. **변경사항 분석**
   - `git status`로 변경된 파일 확인
   - `git diff --cached`로 스테이징된 변경사항 분석
   - 스테이징된 파일이 없으면 경고

2. **커밋 타입 결정**
   - 파일 변경 패턴 분석
   - 추가/수정/삭제된 코드의 성격 파악
   - 적절한 타입(feat/fix/refactor 등) 선택

3. **커밋 메시지 생성**
   - Type과 Scope 결정
   - Subject 작성 (50자 이내, 명령문)
   - 필요시 Body 작성 (72자 줄바꿈)
   - 관련 이슈가 있으면 Footer 추가

4. **사용자 확인**
   - 생성된 커밋 메시지 표시
   - 수정 필요시 재생성 또는 직접 수정 제안
   - 승인 후 커밋 실행

## 커밋 메시지 형식

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Type 종류

- `feat`: 새로운 기능 추가
- `fix`: 버그 수정
- `docs`: 문서 수정
- `style`: 코드 스타일 변경 (포매팅, 세미콜론 등)
- `design`: UI 디자인 변경 (CSS 등)
- `test`: 테스트 코드 추가/수정
- `refactor`: 코드 리팩토링
- `build`: 빌드 시스템, 의존성 수정
- `ci`: CI 설정 파일 수정
- `perf`: 성능 개선
- `chore`: 기타 자잘한 수정
- `rename`: 파일/폴더명 수정
- `remove`: 파일 삭제

### Subject 작성 규칙

- 첫 글자 대문자
- 마침표 없음
- 50자 이내
- 명령문 형태 (Add, Fix, Update 등)
- 한국어: "~추가", "~수정" 형태

### Body 작성 규칙

- 각 줄 72자 이내
- 무엇을, 왜 변경했는지 설명
- 여러 줄 작성 가능

### Footer 작성 규칙

- 관련 이슈: `Fixes: #123`, `Closes: #456`
- Breaking Change: `BREAKING CHANGE: 설명`

## 실행 예시

### 기본 사용

```bash
/auto-commit
```

**실행 결과:**

```
📊 변경사항 분석 중...

📝 스테이징된 파일:
  - src/auth/login.ts (수정)
  - src/auth/token.ts (추가)
  - tests/auth.test.ts (추가)

🤖 생성된 커밋 메시지:

feat(auth): Add JWT token refresh mechanism

- Implement automatic token refresh before expiration
- Add RefreshTokenService class
- Update AuthInterceptor to handle token refresh
- Add unit tests for token refresh logic

Related to: #145

✅ 이 메시지로 커밋하시겠습니까? (y/n/edit)
```

### Dry Run 모드

```bash
/auto-commit --dry-run
```

커밋하지 않고 생성될 메시지만 미리 확인합니다.

### 모든 파일 스테이징 후 커밋

```bash
/auto-commit --stage-all
```

변경된 모든 파일을 자동으로 스테이징한 후 커밋합니다.

### 커밋 후 자동 Push

```bash
/auto-commit --push
```

커밋 완료 후 자동으로 현재 브랜치에 push합니다.

## 분석 로직

### 1. 파일 패턴 기반 Type 결정

```
src/**/*.ts, src/**/*.js → 코드 변경
  - 새 파일 추가 → feat 또는 refactor
  - 버그 수정 패턴 (if 조건 수정, try-catch 추가) → fix
  - 구조 개선 (함수 분리, 중복 제거) → refactor

**/*.test.ts, **/*.spec.ts → test
**/*.css, **/*.scss, **/*.styled.ts → design or style
README.md, docs/** → docs
package.json, tsconfig.json, webpack.config.js → build
.github/workflows/** → ci
```

### 2. Diff 내용 분석

```
+ export class NewService → feat
- throw new Error → fix
+ import { optimize } from → perf
+ // TODO: Refactor → chore
```

### 3. 커밋 분리 제안

여러 타입의 변경이 혼재된 경우 분리 제안:

```
변경사항:
  - src/auth/login.ts (feat)
  - README.md (docs)

💡 제안: 2개의 커밋으로 분리하는 것을 권장합니다.
  Commit 1: feat(auth): Add login feature
  Commit 2: docs: Update README with auth instructions

분리하시겠습니까? (y/n)
```

## 구현 스크립트

다음 단계로 커밋을 실행합니다:

```bash
# 1. 변경사항 확인
git status --short
git diff --cached --name-only

# 2. 스테이징된 파일이 없으면 경고
if [ -z "$(git diff --cached)" ]; then
  echo "⚠️  스테이징된 파일이 없습니다."
  echo "💡 git add <files> 또는 /auto-commit --stage-all 사용"
  exit 1
fi

# 3. Diff 분석 (Claude가 자동 수행)
git diff --cached

# 4. 커밋 메시지 생성 (Claude가 자동 수행)
# - Type 결정
# - Scope 추출
# - Subject 작성
# - Body 작성 (필요시)

# 5. 사용자 확인 후 커밋
git commit -m "생성된 메시지"

# 6. Push (옵션)
if [ "$PUSH" = true ]; then
  git push origin $(git branch --show-current)
fi
```

## 에러 처리

### 스테이징된 파일 없음

```
⚠️  스테이징된 파일이 없습니다.

💡 다음 명령어로 파일을 스테이징하세요:
   git add <files>
   또는
   /auto-commit --stage-all
```

### 커밋 충돌

```
❌ 커밋 실패: merge conflict가 있습니다.

💡 충돌을 해결한 후 다시 시도하세요.
```

### 변경사항이 너무 많음

```
⚠️  50개 이상의 파일이 변경되었습니다.

💡 더 작은 단위로 나누어 커밋하는 것을 권장합니다.
   계속하시겠습니까? (y/n)
```

## 고급 사용법

### 특정 파일만 커밋

```bash
git add src/auth/*.ts
/auto-commit
```

### 커밋 메시지 템플릿 오버라이드

```bash
/auto-commit --template "fix: " 
# → Subject에 "fix: "로 시작하도록 강제
```

### 대화형 스테이징 후 커밋

```bash
git add -p
/auto-commit
```

## 참고 사항

- 이모지는 기본적으로 추가하지 않습니다
- 프로젝트의 기존 커밋 히스토리를 학습하여 일관된 스타일 유지
- 커밋 전에 반드시 사용자 확인을 받습니다
- `--no-verify` 플래그는 자동으로 추가하지 않습니다 (hook 실행 보장)

## 관련 명령어

- `/commit-history` - 최근 커밋 히스토리 분석
- `/git-status` - 변경사항 요약
- `/split-commit` - 큰 커밋을 여러 개로 분리

