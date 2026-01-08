# Auto Commit - 자동 커밋 메시지 생성

자동으로 변경사항을 분석하여 적절한 커밋 메시지를 생성하고 커밋합니다.

## 사용법
```bash
/auto-commit [options]
```

## 옵션

- `--dry-run`: 커밋하지 않고 메시지만 미리보기
- `--no-stage`: 자동 스테이징 없이 이미 스테이징된 파일만 커밋
- `--push`: 커밋 후 자동으로 push
- `--no-body`: Body 없이 Subject만 생성
- `--kor`: Subject와 Body를 한글로 작성 (Type, Scope는 영어 유지)
- `--single`: 모든 변경사항을 하나의 커밋으로 처리

## 기본 동작

- 모든 변경사항을 자동으로 스테이징
- 관련 내용별로 분리하여 여러 개의 커밋 생성
- 커밋 메시지 생성 후 즉시 커밋 실행

## 실행 순서

1. **변경사항 스테이징 (기본)**
   - `git add .`로 모든 변경사항 자동 스테이징
   - `--no-stage` 옵션 시 이 단계 생략

2. **변경사항 분석**
   - `git diff --cached`로 스테이징된 변경사항 분석
   - 스테이징된 파일이 없으면 종료

3. **커밋 그룹 분류 (기본)**
   - Type별로 변경사항 그룹화
   - Scope별로 세부 분류
   - `--single` 옵션 시 모든 변경사항을 하나로 처리

4. **커밋 메시지 생성**
   - 각 그룹별로 Type과 Scope 결정
   - Subject 작성 (50자 이내)
   - Body 작성 (변경 파일 3개 이상 또는 diff 50줄 이상일 때)

5. **커밋 실행**
   - `--dry-run`: 메시지만 표시하고 종료
   - 기본: 각 그룹별로 순차적으로 커밋 실행

## 커밋 분리 기준

| 우선순위 | 기준 | 예시 |
|---------|------|------|
| 1 | Type이 다른 경우 | feat / fix / docs 분리 |
| 2 | 같은 Type 내에서 Scope가 다른 경우 | feat(auth) / feat(api) 분리 |
| 3 | 논리적으로 독립된 기능인 경우 | 로그인 / 회원가입 분리 |

## 커밋 메시지 형식
```
<type>(<scope>): <subject>

<body>
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

### Type 결정 기준

| 파일 패턴 | Type |
|----------|------|
| `src/**/*.ts`, `src/**/*.js` (새 파일) | feat |
| `src/**/*.ts`, `src/**/*.js` (버그 수정 패턴) | fix |
| `src/**/*.ts`, `src/**/*.js` (구조 개선) | refactor |
| `**/*.test.ts`, `**/*.spec.ts` | test |
| `**/*.css`, `**/*.scss`, `**/*.styled.ts` | design |
| `README.md`, `docs/**` | docs |
| `package.json`, `tsconfig.json`, `webpack.config.js` | build |
| `.github/workflows/**` | ci |

### Scope 결정 기준

변경된 파일 경로에서 추출:

| 변경 파일 | Scope |
|----------|-------|
| `src/auth/login.ts` | auth |
| `src/components/Button.tsx` | components |
| 여러 디렉토리에 걸친 변경 | 생략 또는 공통 상위 디렉토리 |
| 루트 파일만 변경 | 생략 |

### Subject 작성 규칙

**영어 (기본)**
- 첫 글자 대문자, 마침표 없음, 50자 이내
- 명령문 형태 (Add, Fix, Update 등)

**한글 (--kor 옵션)**
- 마침표 없음, 50자 이내
- "~추가", "~수정", "~개선" 형태

### Body 작성 규칙

- 변경 파일 3개 이상 또는 diff 50줄 이상일 때 자동 생성
- 각 줄 72자 이내
- `--no-body` 옵션으로 생략 가능

## 실행 예시

### 기본 사용 (분리 커밋)
```bash
/auto-commit
```
```
커밋 1/3 완료: feat(auth): Add JWT token refresh mechanism
커밋 2/3 완료: fix(api): Fix user data validation error
커밋 3/3 완료: docs: Update API documentation

총 3개 커밋 완료
```

### 미리보기 (--dry-run)
```bash
/auto-commit --dry-run
```
```
생성될 커밋 (3개):

[1/3] feat(auth): Add JWT token refresh mechanism
  - src/auth/login.ts
  - src/auth/token.ts

[2/3] fix(api): Fix user data validation error
  - src/api/user.ts

[3/3] docs: Update API documentation
  - README.md

[dry-run] 커밋이 실행되지 않았습니다.
```

## 에러 처리

### 변경사항 없음
```
[경고] 커밋할 변경사항이 없습니다.
```

### 커밋 충돌
```
[오류] 커밋 실패: merge conflict가 있습니다.
[안내] 충돌을 해결한 후 다시 시도하세요.
```

## 참고 사항

- 기본적으로 관련 내용별로 분리하여 여러 개의 커밋 생성
- 하나의 커밋으로 처리하려면 `--single` 옵션 사용
- 미리보기가 필요하면 `--dry-run` 옵션 사용
- 기본 언어는 영어이며, `--kor` 옵션으로 한글 사용 가능
- Type과 Scope는 항상 영어로 유지