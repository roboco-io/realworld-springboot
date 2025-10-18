# Chapter 2: 테스트 커버리지 측정 도입

## 개요

이 챕터에서는 JaCoCo를 활용하여 테스트 커버리지 측정 시스템을 도입합니다. 단순히 도구를 추가하는 것을 넘어, 커버리지 기준 설정, 자동화된 리포트 생성, 품질 게이트까지 구축합니다.

## 학습 목표

- ✅ JaCoCo 테스트 커버리지 도구 설정
- ✅ 커버리지 기준 및 품질 게이트 설정
- ✅ 자동화된 리포트 생성
- ✅ 커버리지 분석 및 개선 방향 도출

## 실제 작업 내용

**커밋**: [test: JaCoCo를 통한 테스트 커버리지 측정 도입](https://github.com/roboco-io/realworld-springboot/commit/fff882e)

### 작업 통계

- **최종 커버리지**: 78% (목표 70% 초과 달성)
- **총 테스트**: 80개
- **통과 테스트**: 78개 (97.5%)
- **소요 시간**: 약 3분
- **수동 작업 대비 절감**: 약 90% (추정 2시간 → 12분)

## 핵심 프롬프트

### 1단계: 문제 인식

```
이 프로젝트의 테스트 자동화에 대해서 검토해줘.
```

Claude Code는 다음을 자동으로 분석:
- 기존 테스트 존재 여부 (`src/test/`)
- 테스트 프레임워크 (JUnit 5)
- 커버리지 도구 유무 (JaCoCo 미설정 확인)
- 테스트 실행 설정

### 2단계: 구체적 작업 요청

```
우선 커버리지 측정부터 해 보자. 이슈 생성하고 진행해줘.
```

## Claude Code의 작업 과정

### 1. 프로젝트 상태 분석

```bash
# 자동으로 실행된 분석
- 테스트 파일 검색: 80개 테스트 발견
- build.gradle 확인: JaCoCo 미설정 확인
- 테스트 실행: ./gradlew test
```

**발견 사항**:
- ✅ 테스트 존재 (80개)
- ✅ JUnit 5 사용
- ❌ 커버리지 측정 도구 없음
- ❌ 커버리지 기준 없음

### 2. 솔루션 제안 및 구현

#### build.gradle 수정

```gradle
plugins {
    id 'java'
    id 'org.springframework.boot' version '2.6.7'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'jacoco'  // JaCoCo 플러그인 추가
}

// JaCoCo 버전 설정
jacoco {
    toolVersion = "0.8.9"
}

// 테스트 실행 시 JaCoCo 에이전트 활성화
test {
    useJUnitPlatform()
    finalizedBy jacocoTestReport  // 테스트 후 자동으로 리포트 생성
}

// JaCoCo 리포트 설정
jacocoTestReport {
    dependsOn test

    reports {
        xml.required = true
        html.required = true  // HTML 리포트 생성
        xml.destination file("${buildDir}/reports/jacoco/test/jacocoTestReport.xml")
        html.destination file("${buildDir}/reports/jacoco/test/html")
    }

    afterEvaluate {
        // 커버리지 제외 대상
        classDirectories.setFrom(files(classDirectories.files.collect {
            fileTree(dir: it, exclude: [
                '**/dto/**',        // DTO 클래스
                '**/entity/**',     // JPA 엔티티
                '**/model/**',      // 모델 클래스
                '**/configuration/**'  // 설정 클래스
            ])
        }))
    }
}

// 커버리지 검증 규칙
jacocoTestCoverageVerification {
    violationRules {
        rule {
            enabled = true
            element = 'CLASS'

            limit {
                counter = 'LINE'
                value = 'COVEREDRATIO'
                minimum = 0.70  // 최소 70% 커버리지
            }
        }

        rule {
            enabled = true
            element = 'CLASS'

            limit {
                counter = 'LINE'
                value = 'COVEREDRATIO'
                minimum = 0.60  // 클래스별 최소 60%
            }

            // DTO, Entity 등 제외
            excludes = [
                '**.dto.*',
                '**.entity.*',
                '**.model.*',
                '**.configuration.*'
            ]
        }
    }
}

// 빌드 시 커버리지 검증 실행
check.dependsOn jacocoTestCoverageVerification
```

#### .gitignore 업데이트

```gitignore
# JaCoCo 리포트
build/reports/jacoco/
```

#### README.md 업데이트

```markdown
## 테스트 및 품질

### 테스트 실행

# 전체 테스트 실행
./gradlew test

# 특정 테스트 클래스 실행
./gradlew test --tests io.zoooohs.realworld.domain.user.service.UserServiceImplTest

# 특정 테스트 메서드 실행
./gradlew test --tests "*.UserServiceImplTest.testLogin"

### 테스트 커버리지

프로젝트는 JaCoCo를 사용하여 테스트 커버리지를 측정합니다.

# 커버리지 리포트 생성
./gradlew test jacocoTestReport

# 커버리지 기준 검증 (최소 70%)
./gradlew jacocoTestCoverageVerification

# HTML 리포트 확인 (macOS)
open build/reports/jacoco/test/html/index.html

# HTML 리포트 확인 (Linux)
xdg-open build/reports/jacoco/test/html/index.html
```

### 3. 커버리지 측정 및 분석

```bash
# 자동 실행
./gradlew test jacocoTestReport
```

**결과 분석**:

```
Overall Coverage: 78%

- Instructions: 2,674 / 3,426 (78%)
- Branches: 35 / 48 (72%)
- Lines: 402 / 478 (84%)
- Methods: 169 / 198 (85%)
- Classes: 44 / 48 (91%)
```

**도메인별 커버리지**:

| 계층 | 파일 수 | 커버리지 | 상태 |
|------|---------|----------|------|
| Controller | 7 | 100% | ✅ 우수 |
| Service | 11 | 87% | ✅ 양호 |
| Entity | 8 | 77% | ✅ 양호 |
| DTO | 12 | 74% | ✅ 양호 |
| Exception | 4 | 96% | ✅ 우수 |
| Security | 5 | 37% | ⚠️ 개선 필요 |

### 4. 개선 권장사항 제시

Claude Code가 자동으로 제안한 개선사항:

```markdown
## 커버리지 개선 권장사항

### 1. Security 계층 (현재 37%)
- `JWTAuthFilter`: 필터 체인 테스트 추가
- `JwtUtils`: JWT 생성/검증 엣지 케이스 테스트
- `WebSecurityConfiguration`: 보안 설정 통합 테스트

### 2. Exception Handling
- 글로벌 예외 핸들러 테스트
- 커스텀 예외 시나리오 테스트

### 3. Integration Tests
- E2E 시나리오 테스트
- API 계약 테스트 (Spring REST Docs)
```

## 핵심 패턴 및 기법

### 1. 점진적 도입 전략

```
현상 파악 → 기초 설정 → 측정 → 분석 → 개선 계획
```

한 번에 100% 커버리지를 목표로 하지 않고:
1. 먼저 현재 상태 파악 (78%)
2. 현실적인 목표 설정 (70%)
3. 개선 로드맵 수립

### 2. 스마트한 제외 설정

모든 코드를 커버리지에 포함할 필요는 없습니다:

```gradle
exclude: [
    '**/dto/**',           // 단순 데이터 객체
    '**/entity/**',        // JPA 엔티티 (Lombok 생성 코드)
    '**/model/**',         // 도메인 모델
    '**/configuration/**'  // 설정 클래스
]
```

**제외 기준**:
- Lombok 생성 코드 (getter/setter)
- Spring 설정 클래스
- 단순 데이터 객체
- 인프라 코드

### 3. 자동화된 워크플로우

```gradle
test {
    finalizedBy jacocoTestReport  // 테스트 후 자동 리포트
}

check {
    dependsOn jacocoTestCoverageVerification  // 빌드 시 검증
}
```

**이점**:
- 개발자가 수동으로 실행할 필요 없음
- CI/CD 파이프라인에 자동 통합
- 품질 게이트로 작동

### 4. 다층 기준 설정

```gradle
rule {
    // 전체 프로젝트 기준
    element = 'BUNDLE'
    minimum = 0.70  // 70%
}

rule {
    // 클래스별 기준
    element = 'CLASS'
    minimum = 0.60  // 60%
}
```

### 5. 시각적 리포트

JaCoCo HTML 리포트는 다음을 제공:
- 📊 전체 커버리지 대시보드
- 📁 패키지별 상세 분석
- 📄 클래스별 라인 커버리지
- 🎨 색상 코딩 (빨강: 미커버, 초록: 커버)

## 실습

### 실습 1: JaCoCo 기본 설정

자신의 프로젝트에 JaCoCo 추가:

```
JaCoCo 테스트 커버리지 측정을 도입하고 싶어.
build.gradle에 JaCoCo 플러그인을 추가하고,
테스트 실행 시 자동으로 리포트가 생성되도록 설정해줘.
이슈를 생성하고 작업을 진행한 다음 PR을 만들어줘.
```

### 실습 2: 커버리지 기준 설정

```
JaCoCo 커버리지 검증 규칙을 추가해줘.
전체 프로젝트는 최소 70% 커버리지를,
개별 클래스는 최소 60% 커버리지를 요구하도록 설정해줘.
DTO, Entity, Configuration 클래스는 제외해줘.
```

### 실습 3: 커버리지 분석

```
현재 프로젝트의 테스트 커버리지를 측정하고 분석해줘.
도메인별, 계층별로 커버리지를 리포트하고,
개선이 필요한 영역과 개선 방안을 제안해줘.
```

## 커버리지 개선 워크플로우

### 1. 현재 상태 파악

```bash
./gradlew test jacocoTestReport
open build/reports/jacoco/test/html/index.html
```

### 2. 낮은 커버리지 영역 식별

HTML 리포트에서:
- 빨간색 영역: 실행되지 않은 코드
- 노란색 영역: 부분적으로 커버된 분기
- 초록색 영역: 완전히 커버된 코드

### 3. 우선순위 설정

**고우선순위**:
- 비즈니스 로직 (Service 계층)
- 보안 관련 코드 (Security)
- 복잡한 알고리즘

**저우선순위**:
- DTO getter/setter
- 단순 CRUD 코드
- 설정 클래스

### 4. 테스트 작성

Claude Code에 요청:

```
JWTAuthFilter의 테스트 커버리지가 낮아.
다음 시나리오에 대한 테스트를 작성해줘:
1. 유효한 JWT 토큰으로 인증 성공
2. 만료된 토큰으로 인증 실패
3. 잘못된 서명의 토큰으로 인증 실패
4. Authorization 헤더가 없는 경우
5. "Bearer " 접두사가 없는 경우
```

### 5. 재측정 및 검증

```bash
./gradlew test jacocoTestReport
./gradlew jacocoTestCoverageVerification
```

## 베스트 프랙티스

### ✅ Do

1. **현실적인 목표**: 70-80% 커버리지가 적정
2. **의미 있는 테스트**: 커버리지보다 품질 우선
3. **자동화**: 테스트 실행 시 자동 리포트 생성
4. **제외 설정**: 불필요한 코드 제외
5. **지속적 모니터링**: CI/CD에 커버리지 체크 통합

### ❌ Don't

1. **100% 집착**: 비현실적이고 비효율적
2. **형식적 테스트**: 커버리지만 높이는 테스트
3. **수동 실행**: 자동화되지 않은 커버리지 측정
4. **일률적 기준**: 모든 코드에 동일한 기준 적용
5. **분석 생략**: 측정만 하고 개선 안 함

## 커버리지 기준 가이드

### 프로젝트 타입별 권장 기준

| 프로젝트 타입 | 최소 커버리지 | 목표 커버리지 |
|---------------|---------------|---------------|
| 금융/의료 | 85% | 90-95% |
| E-commerce | 75% | 80-85% |
| SaaS | 70% | 75-80% |
| 내부 도구 | 60% | 65-70% |
| 프로토타입 | 40% | 50-60% |

### 계층별 권장 기준

| 계층 | 최소 커버리지 | 이유 |
|------|---------------|------|
| Controller | 80% | API 계약 검증 |
| Service | 85% | 핵심 비즈니스 로직 |
| Repository | 60% | Spring Data JPA 활용 |
| Security | 80% | 보안 중요성 |
| DTO/Entity | 50% | Lombok 생성 코드 |

## 다음 단계

- [Chapter 3: Java 21 & Spring Boot 3 업그레이드](./chapter-03-upgrade.md)

테스트 커버리지 측정 시스템이 구축되었으므로, 다음 챕터에서 안전하게 주요 버전 업그레이드를 수행합니다.

## 참고 자료

- [JaCoCo Documentation](https://www.jacoco.org/jacoco/trunk/doc/)
- [Gradle JaCoCo Plugin](https://docs.gradle.org/current/userguide/jacoco_plugin.html)
- [커버리지 리포트 예시](../build/reports/jacoco/test/html/index.html)
- [PR #3: JaCoCo 도입](https://github.com/roboco-io/realworld-springboot/pull/3)

## 핵심 요약

1. **JaCoCo로 테스트 커버리지 자동 측정**
2. **70% 커버리지 달성** (목표 초과)
3. **스마트한 제외 설정**으로 의미 있는 커버리지
4. **자동화된 리포트 생성 및 검증**
5. **개선 로드맵 수립**으로 지속적 품질 향상
