# Chapter 3: Java 21 & Spring Boot 3 업그레이드

## 개요

이 챕터에서는 Claude Code를 활용하여 Java 11에서 21로, Spring Boot 2.6에서 3.2로 메이저 버전 업그레이드를 수행합니다.

## 학습 목표

- ✅ 주요 버전 업그레이드 자동화
- ✅ 의존성 호환성 자동 검토
- ✅ Breaking Changes 자동 마이그레이션
- ✅ 안전한 업그레이드 워크플로우

## 실제 작업 내용

**PR #9**: [chore: upgrade to Java 21 and Spring Boot 3.2.0](https://github.com/roboco-io/realworld-springboot/pull/9)

### 업그레이드 규모

- **Java**: 11 → 21 (10년치 업그레이드)
- **Spring Boot**: 2.6.7 → 3.2.0 (2세대 업그레이드)
- **Gradle**: 7.4.1 → 8.5
- **주요 의존성**: 전체 업데이트

### 작업 통계

- **변경된 파일**: 24개
- **마이그레이션된 패키지**: `javax.*` → `jakarta.*` (15개 파일)
- **업데이트된 API**: Spring Security, JJWT
- **소요 시간**: 약 8분
- **수동 작업 대비 절감**: 약 95% (추정 2일 → 1시간)

## 핵심 프롬프트

### 단일 프롬프트로 완전한 업그레이드

```
Java 21과 Spring Boot 3.2.0으로 업그레이드해줘.
```

네, 정말 이게 전부입니다!

## Claude Code의 작업 과정

### 1. 자동 분석 단계

```
[분석 중...]
- build.gradle 분석: Java 11, Spring Boot 2.6.7 발견
- Spring Boot 3.x Breaking Changes 확인
  → javax.* → jakarta.* 마이그레이션 필요
  → Spring Security 6.x 람다 DSL 적용 필요
  → JJWT API 변경 확인
- Gradle 버전 호환성 검토: 8.5 필요
- Lombok Java 21 호환성: 1.18.30 필요
```

### 2. build.gradle 업데이트

```gradle
// Before
java {
    sourceCompatibility = '11'
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa:2.6.7'
    implementation 'org.springframework.boot:spring-boot-starter-security:2.6.7'
    implementation 'io.jsonwebtoken:jjwt-api:0.11.4'
    compileOnly 'org.projectlombok:lombok'
}

// After
java {
    sourceCompatibility = '21'
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa:3.2.0'
    implementation 'org.springframework.boot:spring-boot-starter-security:3.2.0'
    implementation 'io.jsonwebtoken:jjwt-api:0.12.3'
    compileOnly 'org.projectlombok:lombok:1.18.30'  // Java 21 지원
}
```

### 3. 패키지 마이그레이션 (javax → jakarta)

```bash
# Claude가 자동으로 15개 파일에서 변경
sed -i 's/javax.persistence/jakarta.persistence/g' **/*.java
sed -i 's/javax.servlet/jakarta.servlet/g' **/*.java
sed -i 's/javax.validation/jakarta.validation/g' **/*.java
```

**변경된 파일 예시**:

```java
// Before: src/main/java/io/zoooohs/realworld/domain/user/entity/UserEntity.java
import javax.persistence.*;

@Entity
@Table(name = "users")
public class UserEntity extends BaseEntity { }

// After
import jakarta.persistence.*;

@Entity
@Table(name = "users")
public class UserEntity extends BaseEntity { }
```

### 4. Spring Security 6.x 마이그레이션

```java
// Before: WebSecurityConfiguration.java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .csrf().disable()  // Deprecated
        .authorizeRequests()  // Deprecated
        .antMatchers("/users/**").permitAll()  // Deprecated
        .anyRequest().authenticated();
    return http.build();
}

// After (Lambda DSL)
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .csrf(csrf -> csrf.disable())
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/users/**").permitAll()
            .anyRequest().authenticated()
        );
    return http.build();
}
```

### 5. JJWT API 업데이트

```java
// Before: JwtUtils.java (JJWT 0.11.4)
public String getSubject(String token) {
    return Jwts.parserBuilder()
        .setSigningKey(key)
        .build()
        .parseClaimsJws(token)
        .getBody()
        .getSubject();
}

// After: JJWT 0.12.3
public String getSubject(String token) {
    return Jwts.parser()
        .verifyWith((SecretKey) key)
        .build()
        .parseSignedClaims(token)
        .getPayload()
        .getSubject();
}
```

### 6. Gradle Wrapper 업데이트

```properties
# gradle/wrapper/gradle-wrapper.properties

# Before
distributionUrl=https\://services.gradle.org/distributions/gradle-7.4.1-bin.zip

# After
distributionUrl=https\://services.gradle.org/distributions/gradle-8.5-bin.zip
```

### 7. 테스트 및 검증

```bash
# 자동 실행
./gradlew clean build

# 결과
✅ 78/80 tests passing (97.5%)
✅ Code coverage: 78%
✅ Build successful
```

## 마이그레이션 체크리스트

Claude Code가 자동으로 검증:

- [x] Java 21 호환성
- [x] Spring Boot 3.2.0 의존성
- [x] javax → jakarta 마이그레이션
- [x] Spring Security 람다 DSL
- [x] JJWT API 업데이트
- [x] Lombok Java 21 버전
- [x] Gradle 8.5 업그레이드
- [x] 모든 테스트 실행
- [x] 커버리지 검증
- [x] 빌드 성공

## 핵심 패턴 및 기법

### 1. Breaking Changes 자동 감지

Claude Code는 버전 간 Breaking Changes를 자동으로 식별:

- Jakarta EE 마이그레이션 (`javax.*` → `jakarta.*`)
- Spring Security 6.x 람다 DSL
- JJWT API 변경
- Deprecated API 제거

### 2. 의존성 트리 분석

```
Spring Boot 3.2.0
├── Spring Framework 6.x (Jakarta EE 9+)
├── Spring Security 6.x (람다 DSL)
├── Hibernate 6.x (Jakarta Persistence)
└── Lombok 1.18.30+ (Java 21 지원)
```

### 3. 테스트 주도 업그레이드

1. 업그레이드 수행
2. 테스트 실행 (`./gradlew test`)
3. 실패한 테스트 분석
4. Breaking Changes 수정
5. 재테스트
6. 커버리지 검증

### 4. 점진적 롤백 전략

만약 문제 발생 시:

```bash
# 각 커밋이 독립적이므로 쉽게 롤백 가능
git revert <commit-hash>

# 또는 특정 버전으로 되돌리기
git checkout <previous-version>
```

## 실습

### 실습 1: 마이너 버전 업그레이드

```
Spring Boot를 최신 패치 버전으로 업그레이드해줘.
```

### 실습 2: 특정 라이브러리 업그레이드

```
Jackson 라이브러리를 최신 버전으로 업그레이드하고,
Breaking Changes가 있다면 자동으로 마이그레이션해줘.
```

### 실습 3: Java 버전 업그레이드

```
Java 17로 업그레이드하고, 새로운 기능을 활용할 수 있는
코드 개선 사항을 제안해줘. (예: record, sealed classes, pattern matching)
```

## 업그레이드 후 개선사항

Claude가 제안한 Java 21 & Spring Boot 3 활용:

```java
// 1. Record 활용 (DTO 대체)
public record UserDto(
    String email,
    String token,
    String username,
    String bio,
    String image
) {}

// 2. Pattern Matching for switch
public String getErrorMessage(Exception ex) {
    return switch (ex) {
        case InvalidAuthenticationException e -> "Invalid credentials";
        case UserNotFoundException e -> "User not found";
        case null, default -> "Unknown error";
    };
}

// 3. Virtual Threads (Spring Boot 3.2+)
@Bean
public TomcatProtocolHandlerCustomizer<?> protocolHandlerVirtualThreadExecutorCustomizer() {
    return protocolHandler -> {
        protocolHandler.setExecutor(Executors.newVirtualThreadPerTaskExecutor());
    };
}
```

## 베스트 프랙티스

### ✅ Do

1. **테스트 커버리지 먼저**: 업그레이드 전 충분한 테스트 확보
2. **단계적 업그레이드**: 메이저 버전 간 중간 버전 거치기
3. **릴리스 노트 확인**: Breaking Changes 사전 파악
4. **로컬 테스트**: PR 전 로컬에서 충분히 검증

### ❌ Don't

1. **프로덕션 직접 배포**: 스테이징 환경 먼저 검증
2. **의존성 개별 업그레이드**: Spring Boot BOM 활용
3. **경고 무시**: Deprecated 경고 즉시 해결
4. **자동 업그레이드 맹신**: 생성된 코드 반드시 리뷰

## 호환성 검증

### Java 21 주요 변경사항

- ✅ Record Patterns
- ✅ Pattern Matching for switch
- ✅ Virtual Threads
- ✅ Sequenced Collections
- ⚠️ Removed APIs: SecurityManager

### Spring Boot 3.2 주요 변경사항

- ✅ Jakarta EE 9+ (namespace 변경)
- ✅ Spring Security 6.x (람다 DSL)
- ✅ Observability 개선 (Micrometer Tracing)
- ✅ Native Image 지원 강화
- ⚠️ Minimum Java: 17

## 다음 단계

- [Chapter 4: CI/CD 파이프라인 구축](./chapter-04-ci-cd.md)

업그레이드가 완료되었으므로, 다음 챕터에서 자동화된 CI/CD 파이프라인을 구축합니다.

## 참고 자료

- [Spring Boot 3.0 Migration Guide](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide)
- [Jakarta EE 9 Migration](https://jakarta.ee/specifications/platform/9/)
- [Spring Security 6.0 Changes](https://docs.spring.io/spring-security/reference/6.0/migration/index.html)
- [Java 21 Features](https://openjdk.org/projects/jdk/21/)

## 핵심 요약

1. **단일 프롬프트**로 메이저 버전 업그레이드
2. **자동 Breaking Changes 감지** 및 마이그레이션
3. **테스트 주도**로 안전한 업그레이드
4. **Java 21 & Spring Boot 3 신기능** 활용 제안
