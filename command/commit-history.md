# Commit History - 커밋 히스토리 분석

프로젝트의 커밋 히스토리를 분석하여 커밋 패턴과 컨벤션을 파악합니다.

## 사용법

```bash
/commit-history [옵션]
```

## 옵션

- `--count N`: 최근 N개의 커밋 분석 (기본값: 50)
- `--author <name>`: 특정 작성자의 커밋만 분석
- `--branch <name>`: 특정 브랜치의 커밋 분석
- `--analyze-convention`: 커밋 메시지 컨벤션 분석
- `--stats`: 통계 정보 표시

## 실행 내용

1. **최근 커밋 목록 조회**
   ```bash
   git log --oneline -n 50
   ```

2. **커밋 패턴 분석**
   - 사용된 Type 종류 및 빈도
   - Scope 사용 패턴
   - Subject 길이 및 스타일
   - Body 사용 비율
   - Footer/이슈 연결 비율

3. **컨벤션 도출**
   - 프로젝트의 일관된 패턴 파악
   - 권장 커밋 스타일 제안

## 출력 예시

```
📊 커밋 히스토리 분석 (최근 50개)

📈 Type 분포:
  feat:     18 (36%)
  fix:      12 (24%)
  refactor:  8 (16%)
  docs:      6 (12%)
  chore:     4 (8%)
  test:      2 (4%)

🎯 Scope 사용:
  auth:      10
  api:        8
  ui:         6
  database:   4
  (no scope): 22 (44%)

✍️  Subject 스타일:
  평균 길이:     35자
  명령문 사용:    92%
  첫글자 대문자:  98%
  마침표 있음:    2%

📝 Body 사용률: 24% (12/50)

🔗 Issue 연결: 16% (8/50)

💡 프로젝트 컨벤션:
  - Type은 항상 소문자로 시작
  - Scope 사용률 56%, 주로 모듈명 사용
  - Subject는 명령문 형태, 대문자 시작
  - Body는 복잡한 변경에만 사용
  - Issue 연결은 선택적

✅ 권장 커밋 형식:
type(scope): Subject starting with capital letter

Body (optional, for complex changes)

Fixes: #123 (optional)
```

## 관련 명령어

- `/auto-commit` - 자동 커밋 메시지 생성
- `/split-commit` - 커밋 분리

