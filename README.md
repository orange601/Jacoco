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

#### 1. jacocoTestReport ####
  - 바이너리 커버리지 결과를 HTML로 저장. SonarQube 등으로 연동하기 위해 xml, csv 같은 형태로도 리포트를 생성할 수 있다.

#### 2. jacocoTestCoverageVerification ####
  - 원하는 커버리지 기준을 만족하는지 확인해 주는 task.
  - 예를들어 브랜치 커버리지를 최소한 80% 이상으로 유지하고 싶다면, 이 task에 설정하면 된다. 
  - test task처럼 Gradle 빌드의 성공/실패로 결과를 보여준다.

## 테스트 제외 ##

#### Querydsl Q도메인 제거하기 ####

````gradle
jacocoTestCoverageVerification {
        def Qdomains = []
        // 패키지 + 클래스명
        for (qPattern in '*.QA'..'*.QZ') { // qPattern = '*.QA', '*.QB', ... '*.QZ'
            Qdomains.add(qPattern + '*')
        }

        violationRules {
            rule {
                enabled = true
                element = 'CLASS'

                limit {
                    counter = 'LINE'
                    value = 'COVEREDRATIO'
                    minimum = 0.80
                }

                limit {
                    counter = 'BRANCH'
                    value = 'COVEREDRATIO'
                    minimum = 0.80
                }

                excludes = [] + Qdomains // 제외할 Qdomains 패턴 추가
            }
        }
    }
}
````

- 위와 같이 설정하면 커버리지 측정은 무시할 수 있지만, 여전히 리포트는 수집하여 표시된다.
- 리포트에서도 제거하는 작업을 진행해야 한다.
- 여기서 주의해야할 것이 앞선 작업에서는 **패키지.클래스명**으로 했지만 여기서는 **디렉토리**를 경로로 잡아야 한다.

````gradle
jacocoTestReport {
    dependsOn test // jacocoTestReport 에 test라는 종속성을 추가
    finalizedBy 'jacocoTestCoverageVerification'
    
    reports {
        // 원하는 리포트를 켜고 끌 수 있다.
        html.enabled true
        xml.enabled false
        csv.enabled false
    }
    
    afterEvaluate {
        classDirectories.setFrom(files(classDirectories.files.collect {
            fileTree(dir: it, exclude: [
                "com/blue/green/**Application.class",
                "**/Q*.class", // ( 리포트 무시 )
                //"com/baeldung/**/*DTO.*",
                //"**/config/*"
            ])
        }))
    }
}
````

#### Lombok 제거 ####
1. 루트 디렉토리 lombok.config 파일 생성
2. 설정추가
````
lombok.addLombokGeneratedAnnotation = true
````
