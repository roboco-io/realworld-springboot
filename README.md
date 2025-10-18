# ![RealWorld Example App](logo.png)

> ### Spring Boot 기반 RealWorld 백엔드 API 구현체

[RealWorld](https://github.com/gothinkster/realworld) 사양을 준수하는 완전한 기능을 갖춘 Spring Boot 애플리케이션입니다. Medium 스타일의 소셜 블로깅 플랫폼으로, CRUD 작업, JWT 인증, 팔로우/좋아요, 페이징 등을 포함합니다.

**데모**: [RealWorld](https://realworld-docs.netlify.app/)

## 주요 기능

- ✅ **사용자 관리**: 회원가입, 로그인, 프로필 수정
- ✅ **JWT 인증**: 토큰 기반 인증 및 권한 관리
- ✅ **게시글**: 작성, 조회, 수정, 삭제 (CRUD)
- ✅ **댓글**: 게시글에 댓글 작성 및 관리
- ✅ **소셜 기능**: 팔로우/언팔로우, 게시글 즐겨찾기
- ✅ **태그 시스템**: 게시글 태그 분류 및 필터링
- ✅ **페이징**: 게시글 목록 페이징 및 필터링
- ✅ **피드**: 팔로우한 사용자의 게시글 피드

## 기술 스택

- **Framework**: Spring Boot 2.6.7
- **Language**: Java 11
- **ORM**: Spring Data JPA
- **Security**: Spring Security + JWT
- **Database**:
  - H2 (로컬 개발 및 테스트)
  - MariaDB (프로덕션)
- **Build Tool**: Gradle
- **Other**: Lombok, Validation

## 빠른 시작

### 사전 요구사항

- JDK 11 이상
- Gradle (또는 ./gradlew 사용)

### 애플리케이션 실행

```bash
# 로컬 실행 (H2 인메모리 DB 사용)
./gradlew bootRun
```

애플리케이션이 `http://localhost:8080`에서 실행됩니다.

API Base URL: `http://localhost:8080/api`

### 테스트 실행

```bash
# 전체 테스트 실행
./gradlew test

# 특정 테스트 클래스 실행
./gradlew test --tests io.zoooohs.realworld.domain.user.service.UserServiceImplTest

# 테스트 제외하고 빌드
./gradlew build -x test
```

### 테스트 커버리지

```bash
# 테스트 실행 및 커버리지 리포트 생성
./gradlew test jacocoTestReport

# 커버리지 검증 (최소 70% 기준)
./gradlew jacocoTestCoverageVerification

# 커버리지 리포트 확인
open build/reports/jacoco/test/html/index.html
```

커버리지 리포트는 다음 위치에서 확인할 수 있습니다:
- **HTML**: `build/reports/jacoco/test/html/index.html`
- **XML**: `build/reports/jacoco/test/jacocoTestReport.xml`

### 빌드

```bash
# JAR 파일 생성
./gradlew build

# 생성된 JAR 실행
java -jar build/libs/realworld-0.0.1-SNAPSHOT.jar
```

## 프로젝트 구조

```
src/main/java/io/zoooohs/realworld
├── configuration/      # Spring 설정
├── security/          # JWT 인증 및 보안
├── domain/            # 도메인별 패키지
│   ├── user/         # 사용자 관리
│   ├── profile/      # 프로필 및 팔로우
│   ├── article/      # 게시글, 댓글, 즐겨찾기
│   └── tag/          # 태그
└── exception/         # 예외 처리
```

각 도메인은 다음 계층으로 구성됩니다:
- `controller/` - REST API 엔드포인트
- `service/` - 비즈니스 로직
- `repository/` - 데이터 접근
- `entity/` - JPA 엔티티
- `dto/` - 데이터 전송 객체

## API 문서

### 주요 엔드포인트

**인증**
- `POST /api/users` - 회원가입
- `POST /api/users/login` - 로그인
- `GET /api/user` - 현재 사용자 정보
- `PUT /api/user` - 사용자 정보 수정

**프로필**
- `GET /api/profiles/:username` - 프로필 조회
- `POST /api/profiles/:username/follow` - 팔로우
- `DELETE /api/profiles/:username/follow` - 언팔로우

**게시글**
- `GET /api/articles` - 게시글 목록 (필터링, 페이징)
- `GET /api/articles/feed` - 팔로우 피드
- `GET /api/articles/:slug` - 게시글 조회
- `POST /api/articles` - 게시글 작성
- `PUT /api/articles/:slug` - 게시글 수정
- `DELETE /api/articles/:slug` - 게시글 삭제
- `POST /api/articles/:slug/favorite` - 즐겨찾기
- `DELETE /api/articles/:slug/favorite` - 즐겨찾기 취소

**댓글**
- `GET /api/articles/:slug/comments` - 댓글 목록
- `POST /api/articles/:slug/comments` - 댓글 작성
- `DELETE /api/articles/:slug/comments/:id` - 댓글 삭제

**태그**
- `GET /api/tags` - 태그 목록

상세한 API 명세는 [API 문서](docs/API.md)를 참고하세요.

## 아키텍처

이 프로젝트는 **도메인 주도 설계**와 **계층형 아키텍처**를 따릅니다.

- **Presentation Layer**: REST API 컨트롤러
- **Service Layer**: 비즈니스 로직 및 트랜잭션 관리
- **Repository Layer**: 데이터 접근 및 쿼리
- **Domain Layer**: JPA 엔티티 및 도메인 모델

자세한 내용은 [아키텍처 문서](docs/ARCHITECTURE.md)를 참고하세요.

## 보안

- **JWT 토큰**: 사용자 인증에 JWT 사용
- **BCrypt**: 비밀번호 암호화
- **Spring Security**: 엔드포인트 보안 및 권한 관리
- `/api/users/**` 경로를 제외한 모든 엔드포인트는 인증 필요

JWT 설정은 `src/main/resources/application.yaml`에서 관리됩니다.

## 데이터베이스

### 개발 환경 (H2)
기본적으로 H2 인메모리 데이터베이스를 사용합니다.

### 프로덕션 (MariaDB)
`application.yaml`에서 MariaDB 설정을 구성할 수 있습니다.

## 개발 가이드

### 코딩 컨벤션

- **엔티티**: `{Domain}Entity` (예: `UserEntity`, `ArticleEntity`)
- **DTO**: `{Domain}Dto` (예: `UserDto`, `ArticleDto`)
- **서비스**: `{Domain}Service` 인터페이스, `{Domain}ServiceImpl` 구현
- **리포지토리**: `{Domain}Repository`

### 테스트 전략

- **단위 테스트**: Service 레이어 중심 (`@ExtendWith(MockitoExtension.class)`)
- **통합 테스트**: Controller 레이어 (`@WebMvcTest`, `@WithAuthUser`)
- **리포지토리 테스트**: `@DataJpaTest`

### 테스트 커버리지 정책

- **JaCoCo**를 사용한 자동 커버리지 측정
- **최소 커버리지 목표**: 70% (전체), 60% (클래스별)
- **제외 대상**: DTO, Entity, Configuration, Application 클래스
- 테스트 실행 시 자동으로 커버리지 리포트 생성
- `./gradlew check` 실행 시 커버리지 검증 자동 수행

### 알려진 개선 과제

- [ ] CORS 설정 추가 (`WebSecurityConfiguration.java:26`)
- [ ] UserController 리팩토링 (username 파라미터 사용)
- [ ] 서비스 구조 개선 (1 Service - 1 Repository 원칙)

## 문서

- [API 명세서](docs/API.md) - RESTful API 엔드포인트 상세
- [아키텍처 문서](docs/ARCHITECTURE.md) - 시스템 설계 및 패턴
- [Claude Code 가이드](CLAUDE.md) - AI 개발 도구용 컨텍스트

## 참고 자료

- [RealWorld 사양](https://realworld-docs.netlify.app/)
- [RealWorld GitHub](https://github.com/gothinkster/realworld)
- [Spring Boot 공식 문서](https://spring.io/projects/spring-boot)

## 라이선스

이 프로젝트는 오픈소스이며 MIT 라이선스를 따릅니다. [LICENSE](LICENSE) 파일을 참고하세요.