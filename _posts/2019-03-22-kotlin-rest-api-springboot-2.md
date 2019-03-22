---
layout: post
title: Kotlin과 Spring Boot를 이용한 Rest API 만들기-2
categories: Jekyll, Google Analytics
tags: [Kotlin, Spring Boot, Rest API]
---

**Kotlin**과 **Spring Boot**를 이용한 **Rest API** 서버를 만들기-2

이번 포스팅에선 간단한 계정 정보와 계정 정보에 접근 할 수 있는 간단한 API를 만들어 보겠습니다.

앞의 시간에서 소개해 드렸어야했는데, Restful한 API가 어떤건지에 대한 소개 영상입니다.
- **그런 REST API로 괜찮은가**
    - **<https://youtu.be/RP_f5dMoHFc>**

---------------

- IDE : Intellij IDEA  Ultimate 2018.3.5
- Build : Maven

### github 주소
- 계속해서 작성한 전체 소스는 아래 참고해주세요.
    - <https://github.com/JooWon0712/rest-api-with-springboot-kotlin>

### Account data class 생성

**accounts**라는 패키지 생성 후 **Account**라는 **data class**를 생성 하였습니다.
자바의 경우 클래스 생성 후 getter, setter 등 많은 부분들을 생성 해줘야하지만, Kotlin의 경우 class 앞에 data 선언하게 되면 이를 자동으로 생성해주게 됩니다.

~~~kotlin
package joowon.study.kotlin.accounts

import org.hibernate.annotations.CreationTimestamp
import org.hibernate.annotations.UpdateTimestamp
import java.time.LocalDateTime
import javax.persistence.Column
import javax.persistence.Entity
import javax.persistence.GeneratedValue
import javax.persistence.Id

@Entity
data class Account(
    @Id
    @GeneratedValue
    var id: Int? = null,

    @Column(unique = true)
    var email: String,

    @Column(nullable = false)
    var password: String,

    @CreationTimestamp
    var createDate: LocalDateTime? = null,

    @UpdateTimestamp
    var updateDate: LocalDateTime? = null
)
~~~

### AccountRepository interface 생성

Kotlin에서의 extends와 implements의 선언을 ":" 구분자로 선언합니다.

~~~kotlin
package joowon.study.kotlin.accounts

import org.springframework.data.jpa.repository.JpaRepository

interface AccountRepository : JpaRepository<Account, Long> {
    fun findByEmail(email: String) : Account?
}
~~~

### apllication.properties 설정

hibernate에서 실행되는 쿼리의 포맷팅과 쿼리에 맵핑되는 값들을 확인하려면 아래의 프로퍼티를 설정해주셔야 됩니다.

- **application.properties** 추가

~~~properties
# hibernate
spring.jpa.properties.hibernate.format_sql=true
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
~~~

### 테스트 코드 작성

이제 Account와 AccountRepository의 테스트 코드를 작성해보겠습니다.
Intellij IDEA에선 아래 사진에서와 같이 해당 클래스에 커서를 옴겨 놓고 **CMD + SHIFT + T** 를 누르게 되면 쉽게 해당 클래스의 테스트 클래스를 만들 수 있습니다.

![account-create-test-class]({{ site.baseurl }}/assets/img/account-create-test-class.png)

- **AccountsTests** 테스트 케이스
    - **testAccount** : 새로운 Account 생성 테스트
    - **testAccountSave** : 새로운 Account 생성 후 JPA를 이용하여 insert 하는 케이스 

아래 테스트 케이스중 **testAccountSave**에서 **save()** 후 **findAll()** 을 한 이유는 JPA 테스트의 경우 
**한 트랜잭션이 끝나면 rollback**을 하게 되는데, **EntityManager**가 **save** 후 **rollback** 처리가 되면
처리해야되는 작업이 없어지기 때문에 **save()** 에 해당하는 쿼리가 발생되지 않습니다.
직접 **select** 쿼리를 빼고 어떤 차이가 발생하는지 테스트 해보시는것도 JPA 공부하는데 있어 많은 도움이 되실 것 같습니다.

또 제가 공부할때 수강했던 강좌 링크 첨부하겠습니다.
- 백기선의 스프링 데이터 JPA(**유료 강좌**)
    - <https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-jpa/>

~~~kotlin
package joowon.study.kotlin.accounts

import org.junit.Test
import org.junit.runner.RunWith
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest
import org.springframework.test.context.junit4.SpringRunner

@RunWith(SpringRunner::class)
@DataJpaTest
class AccountsTests {

    @Autowired
    private lateinit var accountRepository: AccountRepository

    @Test
    fun testAccount() {
        val account = Account(null, "test@test.com", "test")
        println(account)
    }

    @Test
    fun testAccountSave() {
        val account = Account(null, "test@test.com", "test")
        accountRepository.save(account)
        accountRepository.findAll()
    }
}
~~~

- **testAccount** 테스트 결과

![AccountsTests-testAccount]({{ site.baseurl }}/assets/img/AccountsTests-testAccount.png)

- **testAccountSave** 테스트 결과
    - 테스트 결과 로그를 자세히 보면 **insert** 쿼리와 **select** 쿼리가 실행된걸 확인 하실수 있습니다.
     
![AccountsTests-testAccountSave]({{ site.baseurl }}/assets/img/AccountsTests-testAccountSave.png)

### AccountController 만들기

구글링을 해보아도 HTTP 메소드만 지원하는 Rest API 예제가 많았습니다.
Restful한 API를 만들기 위해선 **self-descriptive** 와 **hateoas**를 만족해야 진정한 Restful한 API라고 배웠습니다.
제가 배우기로 **self-descriptive**는 Rest API Document를 **profile**링크를 통해 해결할 수 있고, 
**hateoas**는 각각 Http 메소드에 해당하는 링크 정보를 생성하여 응답에 추가하여 제공해주면 된다고 배웠습니다.
**self-descriptive**로 사용할 Rest API Document는 나중에 만들고, 우선은 **Spring Hateoas**를 통해 링크 정보를 포함한 응답 본문을 만들겠습니다.


- pom.xml에 **spring-hateoas** 의존성 추가

~~~xml
<dependency>
    <groupId>org.springframework.hateoas</groupId>
    <artifactId>spring-hateoas</artifactId>
</dependency>
~~~

- AccountResource 클래스 구현
    - Spring Hateoas에서 제공해주는 Resource 클래스
    - 다양한 api를 통해 link 정보를 쉽게 추가할수 있습니다.

~~~kotlin
package joowon.study.kotlin.accounts

import org.springframework.hateoas.Link
import org.springframework.hateoas.Resource

class AccountResource(content: Account?, vararg links: Link?) : Resource<Account>(content, *links) {

}
~~~

- AccountContoller 구현
    - 이번시간에는 HTTP Post 메소드에 대한 응답만 구현하겠습니다.
    - 응답 본문에는 링크 정보가 포함되기 때문에 Accept Header의 셋팅은 **HAL JSON**으로 설정하였습니다.

~~~kotlin
package joowon.study.kotlin.accounts

import org.springframework.beans.factory.annotation.Autowired
import org.springframework.hateoas.MediaTypes
import org.springframework.hateoas.mvc.ControllerLinkBuilder.linkTo
import org.springframework.http.ResponseEntity
import org.springframework.stereotype.Controller
import org.springframework.validation.Errors
import org.springframework.web.bind.annotation.PostMapping
import org.springframework.web.bind.annotation.RequestBody
import org.springframework.web.bind.annotation.RequestMapping
import javax.validation.Valid

@Controller
@RequestMapping("/api/accounts", produces = [MediaTypes.HAL_JSON_UTF8_VALUE])
class AccountController {

    @Autowired
    lateinit var accountRepository: AccountRepository

    @PostMapping
    fun createAccount(@RequestBody @Valid account: Account, errors: Errors) : ResponseEntity<Any> {
        if (errors.hasErrors()) {
            return ResponseEntity.badRequest().body(errors.toString())
        }
        val newAccount = accountRepository.save(account)

        val controllerLinkBuilder = linkTo(AccountController::class.java).slash(newAccount.id)
        val createUri = controllerLinkBuilder.toUri()

        val accountResource = AccountResource(newAccount)
        accountResource.add(linkTo(AccountController::class.java).slash(newAccount.id).withSelfRel())
        accountResource.add(linkTo(AccountController::class.java).slash(newAccount.id).withRel("patch-account"))
        accountResource.add(linkTo(AccountController::class.java).slash(newAccount.id).withRel("delete-account"))

        return ResponseEntity.created(createUri).body(accountResource)
    }
}
~~~

- AccountContollerTests 구현
    - **testCreateAccount()** : 성공에 대한 테스트
    - **testCreateAccount_badRequest()** : 실패에 대한 테스트
    
~~~kotlin
package joowon.study.kotlin.accounts

import org.junit.Test
import org.junit.runner.RunWith
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc
import org.springframework.boot.test.context.SpringBootTest
import org.springframework.hateoas.MediaTypes
import org.springframework.http.MediaType
import org.springframework.test.context.junit4.SpringRunner
import org.springframework.test.web.servlet.MockMvc
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post
import org.springframework.test.web.servlet.result.MockMvcResultHandlers.print
import org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath
import org.springframework.test.web.servlet.result.MockMvcResultMatchers.status

@RunWith(SpringRunner::class)
@SpringBootTest
@AutoConfigureMockMvc
class AccountControllerTests{

    @Autowired
    private lateinit var mockMvc: MockMvc

    @Test
    fun testCreateAccount() {

        val accountJson = "{\"email\" : \"test@test.com\", \"password\" : \"1234\"}"

        mockMvc.perform(post("/api/accounts")
                    .contentType(MediaType.APPLICATION_JSON_UTF8)
                    .accept(MediaTypes.HAL_JSON)
                    .content(accountJson))
                .andDo(print())
                .andExpect(status().isCreated)
                .andExpect(jsonPath("links[0].rel").value("self"))
    }

    @Test
    fun testCreateAccount_badRequest() {

        val accountJson = "{\"email\" : \"test@test.com\"}"

        mockMvc.perform(post("/api/accounts")
                    .contentType(MediaType.APPLICATION_JSON_UTF8)
                    .accept(MediaTypes.HAL_JSON)
                    .content(accountJson))
                .andDo(print())
                .andExpect(status().isBadRequest)
    }
}
~~~

- **testCreateAccount()** 테스트 결과
    - 저장된 Account의 정보를 Json으로 제대로 응답 받았습니다.
    - 단 링크에 대한 응답중 제가 원하지 않는 값들이 셋팅되어서 넘어왔습니다.
    - Java로 구현했을땐 **links** 부분도 **_links**로 표현이 됐었고, null에 해당하는 데이터 부분도 없었습니다.
    - Java와 Kotlin의 차이인지, 아니면 다른 원인이 있는건지 지금 현재는 잘 모르겠습니다(저도 공부하는 입장이라서요ㅠㅠ)

![testCreateAccount-1]({{ site.baseurl }}/assets/img/testCreateAccount-1.png)

null에 해당 하는 부분 응답에서 제외하기 (hreflang, media, title, type, deprecation)
- application.properties 새로운 propery 추가하기

~~~properties
spring.jackson.default-property-inclusion=NON_NULL
~~~

제가 원하지 않던 값들이 응답 본문에서 빠진걸 볼 수 있습니다.

![testCreateAccount-2]({{ site.baseurl }}/assets/img/testCreateAccount-2.png)

사실 위에 방법은 위험한 방법인것 같습니다. 응답하는 Json 데이터중 null값에 해당하는 데이터는 그냥 빼버리는거라서요.
아마 다른 방법이 있을것 같은데, 알고 계시다면 알려주시면 감사하겠습니다.

- **testCreateAccount_badRequest()** 테스트 결과
    - password 값이 빠진 상태로 요청을 했기 때문에 json Parsing중 오류가 발생하였습니다.
    - 테스트 결과에 보면 Response Body에 아무런 응답 메세지를 받지 않았습니다.
    - 응답에 Error에 원인에 대한 응답을 추가하려면 JsonSerializer<Errors> 를 상속받아 serialize 를 오버라이딩 하여 구현할 수 있습니다.

![testCreateAccount-2]({{ site.baseurl }}/assets/img/testCreateAccount-2.png)

다음 포스팅에서 Get, Put(Patch), Delete 메소드에 대해 작성해보겠습니다.

### 번외
저도 사실 Kotlin의 문법을 제대로 알고 하는 상황이 아닙니다. 기본적인 문법을 보고, 뭐라도 만들어보고자
기존에 JAVA로 공부했던 내용들을 Kotlin으로 다시 해보는 상황입니다.
Kotlin으로 구현하는데 있어 IDE의 도움을 전적으로 받아서 하고 있습니다.
중간중간 부족한 부분이 보이신다면 너그러운 마음으로 이해해주셨으면 감사하겠습니다.