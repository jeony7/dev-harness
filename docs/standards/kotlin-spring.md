# Kotlin / Spring 고정 기본값

## 도구

- Java 21, Kotlin 2.1.21, Spring Boot 3.5.7, Gradle 8.14.3 Kotlin DSL.
- 앱 프로젝트에 공식 Gradle Wrapper를 포함합니다. JVM/Spring 프로젝트를 새로 만들 때 Maven을 선택하지 않습니다.
- ktlint 1.5.0, ktlint Gradle plugin 12.1.2. `ktlintFormat`으로 수정, `ktlintCheck`와 `check`로 검사합니다.
- 버전은 이전 실행 템플릿에서 검증한 기본 조합입니다. 이 문서 하네스에는 실행 코드가 없습니다. 실제 제공 JVM/네트워크 제약과 충돌하면 필요한 부분만 조정합니다.

## 초기 설정: Kotlin/Spring을 선택한 경우

### 기존 환경 확인

앱의 Gradle 프로젝트 폴더에서 확인합니다.

```bash
java -version
./gradlew --version
```

JDK 21, `gradlew`, `gradle/wrapper/`, `build.gradle.kts`, `.editorconfig`와 기존 lint 설정을 확인합니다. 정상 작동하는 프로젝트의 버전·규칙을 유지하고 누락된 부분만 추가합니다. 이 하네스의 `.editorconfig`는 신규 프로젝트에서 재사용할 수 있습니다.

JDK가 없으면 해당 개발 환경의 설치 방식으로 JDK 21을 준비하고 `JAVA_HOME`을 맞춥니다. Wrapper가 없고 시스템 Gradle이 있다면 앱 폴더에서 `gradle wrapper --gradle-version 8.14.3 --distribution-type bin`으로 생성합니다. 둘 다 없으면 공식 Gradle 프로젝트 생성·설치 경로를 확인합니다. Wrapper 파일과 실행 권한을 앱 저장소에 포함합니다.

### ktlint가 없는 경우

아래 항목을 `build.gradle.kts`의 기존 `plugins` 블록과 최상위 설정에 병합합니다. Kotlin/Spring 플러그인·의존성 설정은 유지합니다.

```kotlin
plugins {
    id("org.jlleitschuh.gradle.ktlint") version "12.1.2"
}

ktlint {
    version.set("1.5.0")
}
```

ktlint를 전역 설치할 필요는 없습니다. Gradle 첫 실행 시 필요한 배포본·플러그인·의존성을 다운로드하므로 네트워크 접근이 필요합니다. 다운로드나 호환성 오류가 있으면 해당 오류에 필요한 공식 안내만 확인하고 버전 변경 이유를 기록합니다.

### 설정 검증

```bash
./gradlew ktlintCheck
```

포맷 수정이 필요하면 `./gradlew ktlintFormat` 후 다시 검사하고 변경 내용을 확인합니다. 업무 코드를 작성한 뒤에는 `./gradlew check`로 lint와 테스트를 확인합니다. 실행 결과는 RUNBOOK/REVIEW에 필요한 만큼 기록합니다.

## 패키지

```text
com.example.app/
  Application.kt
  api/
    health/HealthController.kt
    feature/FeatureController.kt
    feature/FeatureRequest.kt
    feature/FeatureResponse.kt
  application/
    service/
      health/HealthService.kt
      feature/FeatureService.kt
    domain/
      health/HealthStatus.kt
      feature/Feature.kt
    infra/
      feature/FeatureRepository.kt
      feature/ExternalClient.kt
```

최상위 업무 패키지는 `api`, `application` 두 개입니다. `application` 안에서 `service`, `domain`, `infra`를 별도 하위 패키지로 분리합니다. 그 아래는 필요에 따라 기능별로 묶습니다. 별도 최상위 `domain`, `infrastructure`, `service`를 만들지 않습니다.

- Controller는 HTTP 입력·검증·상태 코드와 DTO 변환을 담당합니다. SQL·업무 판단을 넣지 않습니다.
- Service는 유스케이스, 업무 규칙, 트랜잭션 경계를 담당합니다.
- 도메인 객체는 `application/domain`, 저장소·외부 Client 구현은 `application/infra`에 둡니다. 실제 교체 필요가 없으면 인터페이스를 의무적으로 추가하지 않습니다.
- `application`이 `api`의 Request/Response에 의존하지 않게 합니다. 도메인이 HTTP/DB/외부 API 구현에 의존하지 않게 하고, 여러 인프라 교체가 필요해지면 적절한 계약을 service/domain 쪽에 둡니다.
- DB 제약과 입력 검증을 명시하고, 금액·시간·상태 변경의 의미를 AC와 맞춥니다. 임의 SQL 문자열에 사용자 입력을 합치지 않습니다.
- 문자열 오류를 숨기기 위한 broad catch를 넣지 않습니다. 외부 서비스 오류는 사용자에게 필요한 정보와 내부 진단 정보를 구분합니다.

## 검증과 배포

- `./gradlew ktlintCheck test` 또는 `./gradlew check`.
- 변경한 업무 규칙과 경계·실패 경로를 테스트합니다. 단순 getter/DTO를 테스트 수 확보용으로 검사하지 않습니다.
- React 빌드를 JAR에 포함할 때는 프론트를 먼저 빌드하고 산출물을 리소스로 포함합니다. 재빌드 시 삭제된 이전 정적 파일이 남지 않게 확인합니다. 실제 빌드·기동 명령은 앱의 README와 실행·운영 안내에 기록합니다.
