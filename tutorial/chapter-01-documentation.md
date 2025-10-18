# Chapter 1: 프로젝트 종합 문서화

## 개요

이 챕터에서는 Claude Code를 활용하여 프로젝트 전체를 체계적으로 문서화하는 방법을 학습합니다. 단순히 README 파일을 작성하는 것을 넘어, API 명세, 아키텍처 문서, AI 친화적인 컨텍스트 파일까지 한 번에 생성합니다.

## 학습 목표

- ✅ 효과적인 문서화 프롬프트 작성
- ✅ GitHub 이슈/PR 워크플로우 자동화
- ✅ AI 컨텍스트 최적화 (CLAUDE.md)
- ✅ 다층 문서 구조 설계

## 실제 작업 내용

**PR #2**: [docs: 프로젝트 종합 문서화](https://github.com/roboco-io/realworld-springboot/pull/2)

### 생성된 문서

1. **`docs/API.md`** (477줄) - 전체 REST API 명세서
2. **`docs/ARCHITECTURE.md`** (335줄) - 시스템 아키텍처 문서
3. **`CLAUDE.md`** (161줄) - AI 도구용 프로젝트 가이드
4. **`README.md`** - 대폭 개선 (192줄 추가/수정)

### 작업 통계

- **총 추가 라인**: 1,150줄
- **소요 시간**: 약 5분
- **수동 작업 대비 절감**: 약 95% (추정 8시간 → 20분)

## 핵심 프롬프트

### 1단계: 초기 요청

```
먼저 이 프로젝트에 대해서 문서화를 진행하려고해.
이 프로젝트는 realworld라는 오픈소스 프로젝트야.
공개된 사양 문서는 https://realworld-docs.netlify.app/introduction/ 에서 찾을 수 있어.
문서화를 위한 티켓을 작성하고, 문서화를 진행한 다음 피처 브랜치에 커밋하고 PR을 생성해줘.
```

### 프롬프트 분석

이 간단한 프롬프트에는 중요한 요소들이 포함되어 있습니다:

1. **목적 명시**: "문서화를 진행하려고해"
2. **컨텍스트 제공**: "realworld 오픈소스 프로젝트"
3. **외부 참조**: 공식 사양 문서 URL 제공
4. **완전한 워크플로우**: 이슈 생성 → 문서 작성 → 커밋 → PR 생성

## Claude Code의 작업 과정

### 1. 프로젝트 분석

Claude Code는 자동으로:
- 프로젝트 구조 파악 (`src/` 디렉토리 탐색)
- 기술 스택 식별 (`build.gradle`, `application.yaml`)
- 도메인 모델 이해 (`entity/`, `repository/` 분석)
- API 엔드포인트 추출 (`controller/` 분석)

### 2. GitHub 이슈 생성

```bash
# Claude가 자동으로 실행한 명령
gh issue create --title "docs: 프로젝트 종합 문서화" \
  --body "RealWorld 사양을 준수하는 Spring Boot 구현체에 대한 포괄적인 문서 작성..."
```

생성된 이슈에는 다음이 포함됨:
- 작업 범위 및 목표
- 체크리스트 (API 문서, 아키텍처 문서, README 개선 등)
- 참고 링크

### 3. 피처 브랜치 생성

```bash
git checkout -b docs/comprehensive-documentation
```

### 4. 문서 작성

#### docs/API.md

모든 REST API 엔드포인트를 체계적으로 문서화:

```markdown
## 인증 (Authentication)

### 회원가입
- **URL**: `POST /api/users`
- **인증 필요**: 아니오
- **요청 본문**:
  ```json
  {
    "user": {
      "username": "jake",
      "email": "jake@jake.jake",
      "password": "jakejake"
    }
  }
  ```
- **성공 응답** (200 OK):
  ```json
  {
    "user": {
      "email": "jake@jake.jake",
      "token": "jwt.token.here",
      "username": "jake",
      "bio": "",
      "image": null
    }
  }
  ```
```

#### docs/ARCHITECTURE.md

시스템 설계 및 주요 패턴 문서화:

```markdown
## 계층형 아키텍처

### Controller 계층
- REST API 엔드포인트 제공
- 요청 검증 및 DTO 변환
- HTTP 응답 생성

### Service 계층
- 비즈니스 로직 구현
- 트랜잭션 관리 (@Transactional)
- 도메인 객체 조율

### Repository 계층
- 데이터 접근 추상화
- Spring Data JPA 활용
- 쿼리 메서드 정의
```

#### CLAUDE.md

AI 도구가 프로젝트를 효율적으로 이해할 수 있도록:

```markdown
## Essential Commands

### Development
```bash
# 애플리케이션 실행
./gradlew bootRun

# 전체 테스트 실행
./gradlew test

# 빌드 (테스트 포함)
./gradlew build
```

## Architecture

### Domain-Driven Structure

프로젝트는 도메인 중심 패키지 구조를 따릅니다:

```
io.zoooohs.realworld
├── domain/
│   ├── user/         # 사용자 도메인
│   ├── profile/      # 프로필/팔로우 도메인
│   ├── article/      # 게시글/댓글/좋아요 도메인
│   ├── tag/          # 태그 도메인
│   └── common/       # 공통 엔티티
```
```

### 5. 커밋 및 PR 생성

```bash
# 자동 커밋
git add .
git commit -m "docs: 프로젝트 종합 문서화

- API 명세서 작성 (docs/API.md)
- 아키텍처 문서 작성 (docs/ARCHITECTURE.md)
- README.md 개선
- Claude Code 가이드 추가 (CLAUDE.md)

🤖 Generated with Claude Code
Co-Authored-By: Claude <noreply@anthropic.com>"

# PR 생성
gh pr create --title "docs: 프로젝트 종합 문서화" \
  --body "..." \
  --base master
```

## 핵심 패턴 및 기법

### 1. 컨텍스트 기반 문서화

Claude Code는 다음을 활용하여 정확한 문서 생성:
- 실제 코드 분석 (Controller, Entity, Service)
- 설정 파일 검토 (application.yaml)
- 외부 참조 (RealWorld 사양 문서)

### 2. 계층적 문서 구조

```
README.md          (개요, 빠른 시작)
    ↓
docs/API.md        (API 사용자용)
docs/ARCHITECTURE.md (개발자/아키텍트용)
CLAUDE.md          (AI 도구용)
```

### 3. CLAUDE.md의 중요성

CLAUDE.md는 단순한 문서가 아닙니다:

**장점**:
- AI가 프로젝트를 빠르게 이해
- 반복적인 컨텍스트 제공 불필요
- 일관된 코드 스타일 유지
- 프로젝트 컨벤션 자동 적용

**포함 요소**:
- 프로젝트 개요
- 핵심 명령어
- 아키텍처 패턴
- 네이밍 컨벤션
- 알려진 이슈/개선사항

### 4. 자동화된 워크플로우

이슈 생성 → 브랜치 생성 → 작업 → 커밋 → PR 생성까지 모두 자동화:

```
User Prompt
    ↓
AI Analysis
    ↓
gh issue create        # GitHub 이슈
    ↓
git checkout -b        # 피처 브랜치
    ↓
Document Generation    # 문서 작성
    ↓
git commit            # 커밋
    ↓
gh pr create          # Pull Request
```

## 실습

### 실습 1: 기본 문서화

자신의 프로젝트에서 시도해보세요:

```
이 프로젝트에 대한 포괄적인 문서를 작성해줘.
README.md를 개선하고, docs/ 폴더에 API 문서와 아키텍처 문서를 추가해줘.
문서화를 위한 이슈를 생성하고, 피처 브랜치에 커밋한 다음 PR을 만들어줘.
```

### 실습 2: CLAUDE.md 생성

```
이 프로젝트를 위한 CLAUDE.md 파일을 생성해줘.
AI 도구가 프로젝트를 효율적으로 이해할 수 있도록
아키텍처, 필수 명령어, 코딩 컨벤션을 포함해줘.
```

### 실습 3: API 문서 자동 생성

특정 도메인만 문서화:

```
user 도메인의 모든 API 엔드포인트에 대한 상세 문서를 작성해줘.
요청/응답 예시, 인증 요구사항, 에러 케이스를 포함해줘.
```

## 베스트 프랙티스

### ✅ Do

1. **외부 참조 제공**: 사양 문서, 가이드 URL 포함
2. **완전한 워크플로우 요청**: 이슈 생성부터 PR까지
3. **목적과 범위 명확히**: "API 문서", "아키텍처 문서" 구체적으로 명시
4. **CLAUDE.md 우선 작성**: 이후 작업의 효율성 향상

### ❌ Don't

1. **모호한 요청**: "문서 작성해줘" (무엇을? 어떻게?)
2. **부분적인 지시**: PR 생성 없이 문서만 작성
3. **컨텍스트 부족**: 프로젝트 특성/사양 정보 미제공
4. **검증 생략**: 생성된 문서의 정확성 확인 필수

## 결과 검증

### 문서 품질 체크리스트

- [ ] 모든 API 엔드포인트 문서화
- [ ] 요청/응답 예시 포함
- [ ] 인증 방식 명시
- [ ] 아키텍처 다이어그램/설명
- [ ] 개발 환경 설정 가이드
- [ ] 코딩 컨벤션 명시
- [ ] 외부 문서 링크 연결

### 생성된 PR 확인

```bash
# PR 상세 확인
gh pr view 2

# 변경된 파일 목록
gh pr diff 2 --name-only

# 추가/삭제된 라인 수
gh pr diff 2 --stat
```

## 다음 단계

- [Chapter 2: 테스트 커버리지 측정 도입](./chapter-02-test-coverage.md)

문서화가 완료되면 다음 챕터에서 테스트 자동화와 커버리지 측정을 도입합니다.

## 참고 자료

- [생성된 API 문서](../docs/API.md)
- [생성된 아키텍처 문서](../docs/ARCHITECTURE.md)
- [CLAUDE.md](../CLAUDE.md)
- [PR #2: 프로젝트 종합 문서화](https://github.com/roboco-io/realworld-springboot/pull/2)
- [RealWorld API Spec](https://realworld-docs.netlify.app/)

## 핵심 요약

1. **단일 프롬프트로 전체 문서화** 가능
2. **CLAUDE.md**는 이후 모든 작업의 효율성을 높임
3. **자동화된 Git 워크플로우**로 생산성 극대화
4. **계층적 문서 구조**로 다양한 독자 대응
