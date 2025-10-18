# 아키텍처 문서

RealWorld Spring Boot 구현체의 시스템 아키텍처 및 설계 패턴을 설명합니다.

## 개요

이 프로젝트는 **도메인 주도 설계(Domain-Driven Design)** 원칙을 따르며, **계층형 아키텍처(Layered Architecture)**를 기반으로 구성되어 있습니다.

## 프로젝트 구조

```
io.zoooohs.realworld
├── configuration/          # Spring 설정 클래스
│   └── JPAAuditingConfiguration.java
├── security/              # 보안 및 인증 관련
│   ├── JwtUtils.java
│   ├── JWTAuthFilter.java
│   ├── AuthenticationProvider.java
│   ├── AuthUserDetails.java
│   ├── UserDetailsServiceImpl.java
│   └── WebSecurityConfiguration.java
├── exception/             # 전역 예외 처리
├── domain/                # 도메인 계층
│   ├── common/           # 공통 엔티티
│   │   └── entity/
│   │       └── BaseEntity.java
│   ├── user/             # 사용자 도메인
│   │   ├── controller/
│   │   ├── service/
│   │   ├── repository/
│   │   ├── entity/
│   │   └── dto/
│   ├── profile/          # 프로필/팔로우 도메인
│   ├── article/          # 게시글/댓글/즐겨찾기 도메인
│   └── tag/              # 태그 도메인
└── RealWorldApplication.java
```

## 계층 구조

### 1. Presentation Layer (Controller)

**역할**: HTTP 요청/응답 처리, DTO 변환

**주요 클래스**:
- `UsersController`: 회원가입, 로그인 (`/users`)
- `UserController`: 사용자 정보 조회/수정 (`/user`)
- `ProfilesController`: 프로필 조회, 팔로우/언팔로우
- `ArticlesController`: 게시글 CRUD, 댓글 CRUD, 즐겨찾기
- `TagController`: 태그 목록 조회

**패턴**:
- `@RestController`를 사용한 RESTful API 구현
- `@Valid`를 통한 요청 검증
- `@AuthenticationPrincipal`로 인증된 사용자 정보 주입

### 2. Service Layer

**역할**: 비즈니스 로직 처리, 트랜잭션 관리

**패턴**:
- 인터페이스-구현체 분리 (예: `UserService` ↔ `UserServiceImpl`)
- `@Transactional`을 통한 트랜잭션 관리
- 도메인 로직의 캡슐화

**주요 서비스**:
- `UserService`: 사용자 관리 (회원가입, 로그인, 정보 수정)
- `ProfileService`: 프로필 조회, 팔로우 관리
- `ArticleService`: 게시글 CRUD, 피드, 즐겨찾기
- `CommentService`: 댓글 CRUD
- `TagService`: 태그 관리

### 3. Repository Layer (Data Access)

**역할**: 데이터베이스 접근 및 쿼리 처리

**패턴**:
- Spring Data JPA 활용
- 인터페이스 기반 선언적 쿼리
- 커스텀 쿼리 메서드 네이밍 컨벤션

**주요 리포지토리**:
- `UserRepository`
- `FollowRepository`
- `ArticleRepository`
- `CommentRepository`
- `FavoriteRepository`
- `TagRepository`

### 4. Domain Layer (Entity)

**역할**: 도메인 모델 정의, 비즈니스 규칙 표현

**주요 엔티티**:

```
BaseEntity (추상 클래스)
├── id: Long
├── createdAt: LocalDateTime
└── updatedAt: LocalDateTime

UserEntity extends BaseEntity
├── username: String (unique)
├── email: String (unique)
├── password: String
├── bio: String
└── image: String

ArticleEntity extends BaseEntity
├── slug: String
├── title: String
├── description: String
├── body: String
├── author: UserEntity (ManyToOne)
├── tagList: List<ArticleTagRelationEntity> (OneToMany)
└── favoriteList: List<FavoriteEntity> (OneToMany)

CommentEntity extends BaseEntity
├── body: String
├── article: ArticleEntity (ManyToOne)
└── author: UserEntity (ManyToOne)

FollowEntity extends BaseEntity
├── follower: UserEntity (ManyToOne)
└── followee: UserEntity (ManyToOne)

FavoriteEntity extends BaseEntity
├── user: UserEntity (ManyToOne)
└── article: ArticleEntity (ManyToOne)
```

## 데이터베이스 스키마

### 주요 테이블

**users**
- 사용자 정보 저장
- username, email은 unique 제약 조건

**articles**
- 게시글 정보 저장
- author_id로 users 테이블과 연결

**comments**
- 댓글 정보 저장
- article_id, author_id로 각각 articles, users와 연결

**follows**
- 팔로우 관계 저장
- follower_id, followee_id로 users 테이블과 연결

**favorites**
- 즐겨찾기 관계 저장
- user_id, article_id로 각각 users, articles와 연결

**tags**
- 태그 정보 저장

**article_tag_relations**
- 게시글과 태그의 다대다 관계 저장

### 주요 관계

- User (1) ↔ (N) Article: 한 사용자는 여러 게시글 작성
- User (1) ↔ (N) Comment: 한 사용자는 여러 댓글 작성
- Article (1) ↔ (N) Comment: 한 게시글에 여러 댓글
- User (N) ↔ (M) Article (Favorite): 다대다 관계 (favorites 테이블)
- User (N) ↔ (M) User (Follow): 자기 참조 다대다 관계 (follows 테이블)
- Article (N) ↔ (M) Tag: 다대다 관계 (article_tag_relations 테이블)

## 보안 아키텍처

### JWT 기반 인증 흐름

```
1. 사용자 로그인/회원가입
   ↓
2. JwtUtils.encode() → JWT 토큰 생성
   ↓
3. 클라이언트에 토큰 반환
   ↓
4. 클라이언트가 요청 시 Authorization 헤더에 토큰 포함
   ↓
5. JWTAuthFilter가 요청 인터셉트
   ↓
6. JwtUtils.validateToken() → 토큰 검증
   ↓
7. JwtUtils.getSub() → username 추출
   ↓
8. AuthenticationProvider.getAuthentication() → 인증 객체 생성
   ↓
9. UserDetailsServiceImpl.loadUserByUsername() → 사용자 정보 로드
   ↓
10. SecurityContext에 인증 정보 설정
   ↓
11. 컨트롤러에서 @AuthenticationPrincipal로 사용자 정보 사용
```

### 주요 보안 컴포넌트

**JwtUtils** (`security/JwtUtils.java`)
- JWT 토큰 생성, 검증, 파싱
- HMAC-SHA 알고리즘 사용
- 설정 파일에서 서명 키와 유효 시간 주입

**JWTAuthFilter** (`security/JWTAuthFilter.java`)
- Spring Security 필터 체인에 등록
- "Token {jwt}" 형식의 Authorization 헤더 파싱
- 토큰 검증 및 SecurityContext 설정

**WebSecurityConfiguration** (`security/WebSecurityConfiguration.java`)
- Spring Security 설정
- `/users/**` 경로는 인증 없이 접근 허용
- 나머지 모든 경로는 인증 필요
- CSRF 비활성화 (RESTful API)
- Form 로그인 비활성화

**AuthUserDetails** (`security/AuthUserDetails.java`)
- Spring Security의 UserDetails 구현
- UserEntity를 Spring Security가 이해할 수 있는 형태로 래핑

## 주요 설계 패턴

### 1. Repository Pattern
- Spring Data JPA를 활용한 데이터 접근 추상화
- 비즈니스 로직과 데이터 접근 로직 분리

### 2. DTO Pattern
- 계층 간 데이터 전송을 위한 객체
- 엔티티 직접 노출 방지
- 요청/응답 형식 제어

**DTO 구조 예시**:
```java
public class UserDto {
    @Getter
    public static class Registration {
        private String username;
        private String email;
        private String password;
    }

    @Getter
    public static class Login {
        private String email;
        private String password;
    }

    @Getter
    public static class Update {
        private String email;
        private String bio;
        private String image;
    }
}
```

### 3. Service Layer Pattern
- 비즈니스 로직의 캡슐화
- 트랜잭션 경계 정의
- 재사용 가능한 비즈니스 컴포넌트

### 4. Layered Architecture
- Presentation → Service → Repository → Database
- 각 계층의 명확한 책임 분리
- 의존성은 상위 계층에서 하위 계층으로만 흐름

## 성능 최적화

### N+1 문제 해결

**@NamedEntityGraph 사용**:
```java
@NamedEntityGraph(
    name = "fetch-author-tagList",
    attributeNodes = {
        @NamedAttributeNode("author"),
        @NamedAttributeNode("tagList")
    }
)
public class ArticleEntity extends BaseEntity {
    @ManyToOne(fetch = FetchType.LAZY)
    private UserEntity author;

    @OneToMany(mappedBy = "article", fetch = FetchType.LAZY)
    private List<ArticleTagRelationEntity> tagList;
}
```

리포지토리에서 EntityGraph를 지정하여 필요한 연관 엔티티를 한 번의 쿼리로 fetch join:
```java
@EntityGraph(value = "fetch-author-tagList")
List<ArticleEntity> findAll();
```

### Lazy Loading 기본 전략
- 모든 연관 관계는 기본적으로 `FetchType.LAZY` 사용
- 필요한 경우에만 명시적으로 fetch join

### JPA Auditing
- `@CreatedDate`, `@LastModifiedDate` 자동 관리
- BaseEntity에서 공통 필드 관리
- `JPAAuditingConfiguration`에서 활성화

## 데이터베이스 설정

### H2 (로컬/테스트)
- 인메모리 데이터베이스
- 빠른 개발 및 테스트 환경

### MariaDB (운영)
- 프로덕션 환경용 RDBMS
- `build.gradle`에 의존성 포함
- `application.yaml`에서 설정 관리

## 확장 고려사항

### 현재 알려진 개선 과제

1. **CORS 설정** (`WebSecurityConfiguration.java:26`)
   - 프런트엔드 통합 시 CORS 정책 추가 필요

2. **UserController 리팩토링**
   - `AuthUserDetails` 대신 `username`을 파라미터로 받도록 개선 검토

3. **서비스 구조 개선** (`ArticleServiceImpl`)
   - 1 Service - 1 Repository 원칙 준수
   - 통합 서비스 패턴 도입 검토

### 향후 확장 가능성

- **캐싱**: Redis를 활용한 조회 성능 개선
- **검색**: Elasticsearch 도입으로 전문 검색 기능
- **비동기 처리**: 알림, 이메일 등 비동기 작업 처리
- **이벤트 기반**: Spring Events를 활용한 도메인 이벤트 처리
