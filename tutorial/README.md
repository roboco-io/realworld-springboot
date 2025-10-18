# Claude Code 바이브 코딩 튜토리얼

이 튜토리얼은 실제 프로젝트에서 Claude Code를 활용하여 수행한 바이브 코딩 작업을 기반으로 작성되었습니다.

## 바이브 코딩이란?

바이브 코딩은 단순히 자연어로 코드를 생산하는 것이 아닙니다. AI의 도움을 얻어 **적은 노력으로 높은 수준의 모던 소프트웨어 엔지니어링을 수행**하는 것입니다:

- 📝 **문서화**: 프로젝트 구조, API, 아키텍처 문서 자동 생성
- 🧪 **테스트 자동화**: 테스트 작성, 커버리지 측정, 품질 게이트 설정
- 🚀 **CI/CD**: GitHub Actions, pre-commit hooks 등 자동화 파이프라인 구축
- 🔧 **IaC**: 인프라 코드화 및 배포 자동화
- ⬆️ **기술 부채 해결**: 의존성 업그레이드, 리팩토링, 성능 최적화

## 튜토리얼 구성

이 튜토리얼은 RealWorld Spring Boot 프로젝트를 개선하는 실제 작업을 따라가며 진행됩니다.

### [Chapter 1: 프로젝트 종합 문서화](./chapter-01-documentation.md)
- 프롬프트 하나로 프로젝트 전체 문서화
- CLAUDE.md 작성으로 AI 컨텍스트 최적화
- GitHub 이슈/PR 워크플로우

**주요 프롬프트**:
```
먼저 이 프로젝트에 대해서 문서화를 진행하려고해.
이 프로젝트는 realworld라는 오픈소스 프로젝트야.
공개된 사양 문서는 https://realworld-docs.netlify.app/introduction/ 에서 찾을 수 있어.
문서화를 위한 티켓을 작성하고, 문서화를 진행한 다음 피처 브랜치에 커밋하고 PR을 생성해줘.
```

### [Chapter 2: 테스트 커버리지 측정 도입](./chapter-02-test-coverage.md)
- JaCoCo 테스트 커버리지 도구 도입
- 커버리지 리포트 생성 및 분석
- 품질 기준 설정

**주요 프롬프트**:
```
이 프로젝트의 테스트 자동화에 대해서 검토해줘.
우선 커버리지 측정부터 해 보자. 이슈 생성하고 진행해줘.
```

### [Chapter 3: Java 21 & Spring Boot 3 업그레이드](./chapter-03-upgrade.md)
- 주요 버전 업그레이드 (Java 11 → 21, Spring Boot 2.6 → 3.2)
- 의존성 호환성 검토
- 마이그레이션 가이드 적용

**주요 프롬프트**:
```
Java 21과 Spring Boot 3.2.0으로 업그레이드해줘.
```

### [Chapter 4: CI/CD 파이프라인 구축](./chapter-04-ci-cd.md)
- GitHub Actions CI 워크플로우 설정
- Git pre-commit hooks로 로컬 품질 검증
- 자동화된 테스트 및 빌드

**주요 프롬프트**:
```
깃헙 액션즈와 함께 로컬에서 git hooks로 pre commit시에 테스트를 돌려서
테스트가 실패하거나 커버리지가 80%에 미달할 경우 커밋이 실패하도록 설정하고싶어.
이를 위한 적절한 솔루션을 제안해줘.
```

## 학습 목표

이 튜토리얼을 완료하면 다음을 할 수 있습니다:

1. ✅ Claude Code를 활용한 프로젝트 문서화
2. ✅ 테스트 자동화 및 커버리지 측정 도입
3. ✅ 주요 버전 업그레이드 수행
4. ✅ CI/CD 파이프라인 구축
5. ✅ Git 워크플로우 최적화
6. ✅ 효과적인 프롬프트 작성 기법

## 시작하기

### 사전 요구사항

- Java 21
- Gradle 7.x 이상
- Git
- GitHub CLI (선택사항)
- Claude Code 또는 Claude API 접근

### 프로젝트 클론

```bash
git clone https://github.com/roboco-io/realworld-springboot.git
cd realworld-springboot
```

### 튜토리얼 진행 방법

각 챕터는 독립적으로 진행할 수 있지만, 순서대로 진행하는 것을 권장합니다.

1. Chapter별 README를 읽고 배경 이해
2. 제시된 프롬프트를 Claude Code에 입력
3. AI의 응답과 생성된 코드 확인
4. 실제 작업 결과와 비교
5. 핵심 패턴과 기법 학습

## 참고 자료

- [RealWorld API Spec](https://realworld-docs.netlify.app/introduction/)
- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [Claude Code Documentation](https://docs.claude.com/claude-code)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

## 기여하기

튜토리얼 개선 제안은 언제나 환영합니다! Issue나 PR을 생성해주세요.

## 라이선스

MIT License
