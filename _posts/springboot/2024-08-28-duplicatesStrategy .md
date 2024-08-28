---

layout: single
title: "duplicatesStrategy = DuplicatesStrategy.EXCLUDE"
toc: true
toc_sticky: true
toc_label: " "
categories: Springboot

---

## 문제상황 및 해결과정

1. swagger-ui 관련 코드는 외부 라이브러리
2. 우리 프로젝트(스프링부트)에선 의존성 추가해서 사용
3. 근데 똑립 버전으로 커스터마이징할 때 swagger-ui에 있는 파일(html, css 등)은 read_only이기 때문에 수정이 불가
4. 그래서 swagger 깃허브 들어가서 오픈소스 다운 받고 우리 프로젝트 resource 부분에 직접 넣어서 커스텀
    
    https://github.com/swagger-api/swagger-ui
    
    ![image](https://github.com/user-attachments/assets/cb359dd0-ffec-4413-8f3c-daf00c4fea4f)
    
5. 로컬에선 프로젝트 내부의 파일(MANIFEST.MF)과 외부 라이브러리(MANIFEST.MF)가 있을 때 따로 설정 안 해줘도 내부의 파일만 동작이 된 것 같음
6. 그래서 머지 했는데 깃헙 액션 에러 로그를 보니 중복 파일에 대한 처리가 필요하다고 뜸
    
    ```
    * What went wrong:
    Execution failed for task ':bootJar'.
    3 actionable tasks: 3 executed
    > Entry META-INF/MANIFEST.MF is a duplicate but no duplicate handling strategy has been set. Please refer to https://docs.gradle.org/8.5/dsl/org.gradle.api.tasks.Copy.html#org.gradle.api.tasks.Copy:duplicatesStrategy for details.
    ```
    
7. build.gradle에 관련 오류 해결을 위한 설정 추가해서 해결
    
    ```
    bootJar {
        mainClass = 'com.api.ttoklip.TtoklipApplication'    
        duplicatesStrategy = DuplicatesStrategy.EXCLUDE      
    }
    ```
    

## `duplicatesStrategy = DuplicatesStrategy.EXCLUDE`

위처럼 bootJar 태스크에서 `duplicatesStrategy = DuplicatesStrategy.EXCLUDE`를 설정한 경우 Gradle은 중복된 파일을 처리할 때 하나만 선택하고 나머지를 제외한다. 

그렇다면 프로젝트 내의 파일과 외부 라이브러리의  파일 중 어떤 것이 사용될까?

### 기본 동작

- **우선 순위**: Gradle은 일반적으로 첫 번째로 발견한 파일을 사용한다. 이는 보통 프로젝트 자체의 리소스가 우선으로 사용된다는 것을 의미한다.
- **결과**: 프로젝트에 있는 `META-INF/MANIFEST.MF` 파일이 먼저 발견되고 포함된다면 이 파일이 최종 빌드된 JAR 파일에 사용된다. 따라서 외부 라이브러리의 파일은 제외된다.

### 결론

- 위 설정으로 인해 프로젝트 내의 파일이 사용될 가능성이 높다. Gradle은 첫 번째로 발견한 파일을 포함시키고 이후 중복된 파일은 제외하는 방식을 따르기 때문!!
- 만약 외부 라이브러리의 파일을 포함시키고 싶다면
    - 프로젝트의 내부의 파일을 제거하거나
    - `duplicatesStrategy` 설정을 변경

### 참고 사항

- 중복 파일 중 특정한 파일을 반드시 사용해야 한다면 해당 파일의 위치를 명시적으로 설정하는 것이 좋다.
- 예를 들어 `jar` 또는 `bootJar` 태스크에서 `manifest` 블록을 사용하여 직접 정의한다.