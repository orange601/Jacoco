# Jacoco
- Gradle 프로젝트에 JaCoCo 설정
- 우아한형제들 기술블로그 참조
- 출처: https://techblog.woowahan.com/2661/

## Jacoco란 ##
- JaCoCo는 Java 코드의 커버리지를 체크하는 라이브러리
- 테스트코드를 돌리고 그 커버리지 결과를 눈으로 보기 좋도록 html이나 xml, csv 같은 리포트로 생성
- 커버리지 기준을 만족시키지 못 하면 배포를 하지 못 하게 할 수 있다.

## Gradle Jacoco 플러그인 ##
- JaCoCo Gradle 플러그인에는 jacocoTestReport와 jacocoTestCoverageVerification task가 있다.

#### jacocoTestReport ####
  - 바이너리 커버리지 결과를 HTML로 저장. SonarQube 등으로 연동하기 위해 xml, csv 같은 형태로도 리포트를 생성할 수 있다.

#### jacocoTestCoverageVerification ####
  - 원하는 커버리지 기준을 만족하는지 확인해 주는 task.
  - 예를들어 브랜치 커버리지를 최소한 80% 이상으로 유지하고 싶다면, 이 task에 설정하면 된다. 
  - test task처럼 Gradle 빌드의 성공/실패로 결과를 보여준다.
