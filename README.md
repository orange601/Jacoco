# Jacoco
  - Gradle 프로젝트 JaCoCo 설정
  - 출처 - [우아한형제들 기술블로그](https://techblog.woowahan.com/2661)

[이클립스용 TOOL 설명](#이클립스용-TOOL-설명)



  
  - 이클립스용 jacoco 툴 : EclEmma
  - 이클립스 Marketplace에 EclEmma를 검색해서 설치할 수 있다.
  - 사이트: https://www.jacoco.org/index.html

## Jacoco란 ##
  - JaCoCo는 Java 코드의 커버리지를 체크하는 라이브러리
  - 테스트코드를 돌리고 그 커버리지 결과를 보기 좋도록 html이나 xml, csv 같은 리포트로 생성
  - 커버리지 기준을 만족시키지 못 하면 배포를 하지 못 하게 할 수 있다.

## Gradle Jacoco 플러그인 ##
  - JaCoCo Gradle 플러그인에는 **jacocoTestReport**와 **jacocoTestCoverageVerification** task가 있다.

#### 1. jacocoTestReport ####
  - 바이너리 커버리지 결과를 HTML로 저장. SonarQube 등으로 연동하기 위해 xml, csv 같은 형태로도 리포트를 생성할 수 있다.

#### 2. jacocoTestCoverageVerification ####
  - 원하는 커버리지 기준을 만족하는지 확인해 주는 task.
  - 예를들어 브랜치 커버리지를 최소한 80% 이상으로 유지하고 싶다면, 이 task에 설정하면 된다. 
  - test task처럼 Gradle 빌드의 성공/실패로 결과를 보여준다.


## jacocoTestCoverageVerification Rule ##

````gradle
jacocoTestCoverageVerification {
	def Qdomains = []
	// 패키지 + 클래스명
	for (qPattern in '*.QA'..'*.QZ') { // qPattern = '*.QA', '*.QB', ... '*.QZ'
	    Qdomains.add(qPattern + '*')
	}
	
	violationRules {
		rule {
			// 룰을 간단히 켜고 끌 수 있다.
			enabled = true;
			// 룰을 체크할 단위는 클래스 단위
			element = 'CLASS'
			
			limit {
			  counter = 'BRANCH' // 조건문 등의 분기 수
			  value = 'COVEREDRATIO'
			  minimum = 0.90 // 브랜치 커버리지를 최소한 90% 만족시켜야 합니다.
			}

			//limit {
				//counter = 'METHOD' // 메서드 수, 메서드가 한 번이라도 실행된다면 실행된 것으로 간주
				//value = 'COVEREDRATIO'
				//minimum = 0.3
			//}
			
			excludes = [] + Qdomains // 제외할 Qdomains 패턴 추가 ( 측정 무시 )
		}

	}
}
````

#### enable ####
- 활성화 여부

#### element ####
- 커버리지를 체크할 기준(단위)을 정할 수 있으며, 총 6개의 기준이 존재
1. BUNDLE : 패키지 번들(프로젝트 모든 파일을 합친 것)
2. CLASS : 클래스
3. GROUP : 논리적 번들 그룹
4. METHOD : 메서드
5. PACKAGE : 패키지
6. SOURCEFILE : 소스 파일

값을 지정하지 않는 경우 Default 값은 BUNDLE

#### counter ####
- counter 는 limit 메서드를 통해 지정할 수 있으며 커버리지 측정의 최소 단위
- 측정은 java byte code가 실행된 것을 기준으로 측정되고, 총 6개의 단위가 존재
1. BRANCH : 조건문 등의 분기 수
2. CLASS : 클래스 수, 내부 메서드가 한 번이라도 실행된다면 실행된 것으로 간주한다.
3. COMPLEXITY : 복잡도
4. INSTRUCTION : Java 바이트코드 명령 수
5. METHOD : 메서드 수, 메서드가 한 번이라도 실행된다면 실행된 것으로 간주한다.
6. LINE : 빈 줄을 제외한 실제 코드의 라인 수, 라인이 한 번이라도 실행되면 실행된 것으로 간주한다.

Default 값은 INSTRUCTION

#### value ####
- value 는 limit 메서드를 통해 지정할 수 있으며 측정한 커버리지를 어떠한 방식으로 보여줄 것인지를 의미
- 총 5개의 방식이 존재
1. COVEREDCOUNT : 커버된 개수
2. COVEREDRATIO : 커버된 비율, 0부터 1사이의 숫자로 1이 100%이다.
3. MISSEDCOUNT : 커버되지 않은 개수
4. MISSEDRATIO : 커버되지 않은 비율, 0부터 1사이의 숫자로 1이 100%이다.
5. TOTALCOUNT : 전체 개수

Default 값은 COVEREDRATIO

#### minimum ####
- minimum 은 limit 메서드를 통해 지정할 수 있으며 counter 값을 value 에 맞게 표현했을 때 최솟값을 의미
- 이 값을 통해 jacocoTestCoverageVerification 의 성공 여부가 결정
- 해당 값은 BigDecimal 타입이고 표기한 자릿수만큼 value가 출력
- 만약 커버리지를 80%를 원했는데 0.80이 아니라 0.8을 입력하면 커버리지가 0.87이라도 0.8로 표시

#### excludes ####
- 제외할 클래스를 지정

## excludes(테스트 제외) ##

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
2. 아래 lombok 설정추가
````
lombok.addLombokGeneratedAnnotation = true
````


## 이클립스용 TOOL 설명 ##
