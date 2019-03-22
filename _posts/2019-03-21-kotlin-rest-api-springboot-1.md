---
layout: post
title: Kotlin과 Spring Boot를 이용한 Rest API 만들기-1
categories: Jekyll, Google Analytics
tags: [Kotlin, Spring Boot, Rest API]
---

**Kotlin**과 **Spring Boot**를 이용한 **Rest API** 서버를 만들기-1

이번 포스팅에선 간단한 Controller 생성과 테스트로 시작해보겠습니다.

- IDE : Intellij IDEA  Ultimate 2018.3.5
- Build : Maven

### github 주소
- 계속해서 작성한 전체 소스는 아래 참고해주세요.
    - <https://github.com/JooWon0712/rest-api-with-springboot-kotlin>

### Spring Initializer를 이용한 프로젝트 생성
- New Project -> Spring Initializer 선택
    - Service URL : Default
- Project SDK : 전 OpenJDK 11버전이 설치돼있습니다.
    - OracleJDK, OpenJDK 둘다 1.8이상 되어야합니다.(Spring Boot 2.x 버전의 최소 스펙)

![kotlin-rest-api-init-1]({{ site.baseurl }}/assets/img/kotlin-rest-api-init-1.png)

- Group : 적당한 Group ID 입력
- Artifact : 적당한 Artifact ID 입력
- Language : **Kotlin** 선택
- Java Version : 프로젝트의 Java Version을 선택(컴파일러의 버전)
    - Project SDK는 JDK 11로 설정 됐지만, 이 옵션에서 선택된 버전의 구문과 컴파일러 버전을 사용함.
    - Kotlin을 사용하여 소스 코드를 작성하고 코틀린 컴파일러가 해당 버전에 맞는 Java Byte Code를 생성함.

![kotlin-rest-api-init-2]({{ site.baseurl }}/assets/img/kotlin-rest-api-init-2.png)

- 의존성 설정
![kotlin-rest-api-init-3]({{ site.baseurl }}/assets/img/kotlin-rest-api-init-3.png)


### start.spring.io 를 이용한 프로젝트 생성
Intellij Idea Ultimate 버전이 아닌 경우 아래 방법으로도 프로젝트를 생성

- <https://start.spring.io/>
- Generate Project
    - Language : Kotlin
    - More options : Packaging, Java Version 등 설정
    - Dependencies : Web, JPA, H2

- **Generate Project** 버튼을 클릭하면 .zip 파일을 다운로드 받게 되고, 해당 zip 압축 해제 후 프로젝트 디렉토리에 복사
    
![kotlin-rest-api-init-4]({{ site.baseurl }}/assets/img/kotlin-rest-api-init-4.png)

### 프로젝트 생성 후 HelloWorld 출력 해보기

1. HelloController 생성.
- 자바에선 패키지가 해당 소스의 Path 경로에 맞게 설정돼 있어야 하지만, Kotlin는 해당 소스의 Path와 상관없이 선언할수 있습니다.
- 하지만 개인적으론 자바와 같이 패키지 설정 및 관리를 해주는게 좋다고 생각합니다.

~~~kotlin
package joowon.study.kotlin.hello

import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RestController

@RestController
class HelloController {

    @GetMapping("/hello")
    fun helloKotlin() : String {
        return "Hello Kotlin"
    }

}
~~~

2. HelloControllerTests 생성
- mockMvc로 테스트 케이스 작성

~~~kotlin
package joowon.study.kotlin.hello

import org.junit.Test
import org.junit.runner.RunWith
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest
import org.springframework.test.context.junit4.SpringRunner
import org.springframework.test.web.servlet.MockMvc
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get
import org.springframework.test.web.servlet.result.MockMvcResultHandlers.print
import org.springframework.test.web.servlet.result.MockMvcResultMatchers.status

@RunWith(SpringRunner::class)
@WebMvcTest(HelloController::class)
class HelloControllerTests {

    @Autowired
    private lateinit var mockMvc: MockMvc

    @Test
    fun testHello() {
        mockMvc.perform(get("/hello"))
                .andDo(print())
                .andExpect(status().isOk)
    }
}
~~~

### Test 결과
- <code>HelloController</code>의 맵핑된 <code>/hello</code>의 응답을 잘 받아오는걸 확인할 수 있습니다.

![kotlin-hello-controller-test]({{ site.baseurl }}/assets/img/kotlin-hello-controller-test.png)

### 번외

- **spring.io**에서 Kotlin과 Spring Boot를 이용하여 웹어플리케이션을 만드는 공 튜토리얼 문서가 있습니다.
    - <https://spring.io/guides/tutorials/spring-boot-kotlin/>

튜토리얼 문서를 보면서 따라하다보면 **spring-boot-starter-test**에서 기본적으로 제공되는 **junit4**의 의존성을 제외하고, **junit5**를 의존성을 별도로 설정하라고 나옵니다.
처음엔 튜토리얼에서 가이드 해주는것처럼 따라서 해보았지만, 테스트로 만든 컨트롤러를 **MockMvc**로 테스트 하려고 해보았지만, **junit5**에서 오류가 발생하는 원인을 찾지 못해 **junit4**로 테스트 케이스를 작성하였습니다.

- junit4, junit5의 의존성 설정(백기선님의 유튜브)
    - <https://youtu.be/TTkJc8xfax8>

- **코틀린 무료 강좌**
    - <https://www.inflearn.com/course/%EC%BD%94%ED%8B%80%EB%A6%B0-%EA%B0%95%EC%A2%8C-%EC%83%88%EC%B0%A8%EC%9B%90/>