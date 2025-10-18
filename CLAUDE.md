# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

RealWorld 명세를 준수하는 Spring Boot 기반 백엔드 API 구현체입니다. Medium 스타일의 소셜 블로깅 플랫폼으로 사용자 인증, 게시글 CRUD, 팔로우/좋아요, 태그 등의 기능을 제공합니다.

**Technology Stack**:
- Spring Boot 2.6.7 (Java 11)
- Spring Data JPA + Spring Security
- JWT 인증 (jjwt 0.11.4)
- H2 (로컬/테스트) / MariaDB (운영)
- Lombok

## Essential Commands

### Development
```bash
# 애플리케이션 실행 (H2 인메모리 DB 사용)
./gradlew bootRun

# 전체 테스트 실행
./gradlew test

# 특정 테스트 클래스 실행
./gradlew test --tests io.zoooohs.realworld.domain.user.service.UserServiceImplTest

# 특정 테스트 메서드 실행
./gradlew test --tests io.zoooohs.realworld.domain.user.service.UserServiceImplTest.testMethodName

# 빌드 (테스트 포함)
./gradlew build

# 빌드 (테스트 제외)
./gradlew build -x test
```

### API Access
- Base URL: `http://localhost:8080/api`
- 인증 헤더: `Authorization: Token {JWT_TOKEN}`
- JWT 설정: `src/main/resources/application.yaml`

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
│   └── common/       # 공통 엔티티 (BaseEntity)
├── security/         # JWT 인증/인가
├── configuration/    # Spring 설정
└── exception/        # 전역 예외 처리
```

각 도메인은 다음과 같은 계층 구조를 가집니다:
- `controller/` - REST API 엔드포인트
- `service/` - 비즈니스 로직
- `repository/` - 데이터 접근 계층
- `entity/` - JPA 엔티티
- `dto/` - 요청/응답 데이터 전송 객체
- `model/` - 도메인 모델 (일부 도메인만 해당)

### Security Architecture

**JWT 기반 인증 흐름**:
1. `JWTAuthFilter` - HTTP 요청에서 "Token {jwt}" 형식의 Authorization 헤더 추출
2. `JwtUtils` - JWT 검증 및 subject(username) 추출
3. `AuthenticationProvider` - username으로 인증 객체 생성
4. `UserDetailsServiceImpl` - UserEntity 로드 및 AuthUserDetails 생성
5. SecurityContext에 인증 정보 설정

**주요 클래스**:
- `JwtUtils` - JWT 생성/검증/파싱 (HMAC 서명, 만료 시간 검증)
- `JWTAuthFilter` - Spring Security 필터 체인에 통합
- `WebSecurityConfiguration` - `/users/**` 경로만 인증 없이 허용, 나머지는 인증 필요
- `AuthUserDetails` - Spring Security UserDetails 구현체

### Data Model Key Patterns

**BaseEntity 상속**:
모든 엔티티는 `BaseEntity`를 상속받아 공통 필드를 공유합니다:
- `id` (Long, auto-increment primary key)
- `createdAt` (LocalDateTime, @CreatedDate)
- `updatedAt` (LocalDateTime, @LastModifiedDate)

JPA Auditing은 `JPAAuditingConfiguration`에서 활성화됩니다.

**주요 엔티티 관계**:
- `UserEntity` (1) ↔ (N) `ArticleEntity` (author)
- `UserEntity` (1) ↔ (N) `FollowEntity` (follower/followee)
- `ArticleEntity` (1) ↔ (N) `CommentEntity`
- `ArticleEntity` (1) ↔ (N) `FavoriteEntity` (좋아요)
- `ArticleEntity` (1) ↔ (N) `ArticleTagRelationEntity` ↔ (1) `TagEntity`

**N+1 문제 해결**:
`ArticleEntity`는 `@NamedEntityGraph`를 사용하여 author와 tagList를 fetch join으로 조회합니다:
```java
@NamedEntityGraph(name = "fetch-author-tagList",
    attributeNodes = {@NamedAttributeNode("author"), @NamedAttributeNode("tagList")})
```

### Service Layer Patterns

서비스 계층은 인터페이스와 구현체로 분리되어 있습니다 (예: `UserService` ↔ `UserServiceImpl`).

**주요 서비스**:
- `UserService` - 회원가입, 로그인, 프로필 수정
- `ProfileService` - 팔로우/언팔로우, 프로필 조회
- `ArticleService` - 게시글 CRUD, 페이징, 필터링
- `CommentService` - 댓글 CRUD
- `TagService` - 태그 목록 조회

서비스 레이어에서 비즈니스 로직을 처리하고, 컨트롤러는 DTO 변환과 HTTP 응답만 담당합니다.

## Development Guidelines

### Testing Strategy

- 단위 테스트: Service 레이어 중심 (`@ExtendWith(MockitoExtension.class)`)
- 통합 테스트: Controller 레이어 (`@WebMvcTest` + `@WithAuthUser`)
- 리포지토리 테스트: `@DataJpaTest` 사용

**커스텀 테스트 어노테이션**:
- `@WithAuthUser` - 인증된 사용자 컨텍스트 생성 (테스트용)
- `WithAuthUserSecurityContextFactory` - SecurityContext 설정

### Known TODOs

다음 개선 사항들이 코드에 TODO로 표시되어 있습니다:

1. **CORS 설정** (`WebSecurityConfiguration:26`): CORS 허용 관련 내용 추가 필요
2. **UserController 리팩토링** (`UserController`): `userService.getUser(userId: String)`로 변경 검토
3. **서비스 구조 개선** (`ArticleServiceImpl`): 1 service - 1 repository 구조로 통합 서비스 패턴 고려

### Naming Conventions

- Entity 클래스: `{Domain}Entity` (예: `UserEntity`, `ArticleEntity`)
- DTO 클래스: `{Domain}Dto` (예: `UserDto`, `ArticleDto`)
- Service 인터페이스: `{Domain}Service`
- Service 구현체: `{Domain}ServiceImpl`
- Repository: `{Domain}Repository`

### Database Configuration

`application.yaml`에서 데이터베이스 설정을 관리합니다. 기본적으로 H2 인메모리 DB를 사용하며, 운영 환경에서는 MariaDB로 전환 가능합니다.

JWT 설정도 동일 파일에서 관리:
- `realworld.auth.token.sign-key`: JWT 서명 키
- `realworld.auth.token.valid-time`: JWT 유효 시간 (밀리초)

### Service Layer Note

`article` 도메인의 service 패키지명이 `servie`로 오타가 있습니다 (`src/main/java/io/zoooohs/realworld/domain/article/servie/`). 기존 코드와의 일관성을 위해 수정 시 패키지 구조 전체를 고려해야 합니다.
