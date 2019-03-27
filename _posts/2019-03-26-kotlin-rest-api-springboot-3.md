---
layout: post
title: Kotlin과 Spring Boot를 이용한 Rest API 만들기-3
categories: Kotlin
tags: [Kotlin]
comments: true
---

**Kotlin**과 **Spring Boot**를 이용한 **Rest API** 서버를 만들기-3

이번 포스팅에선 계정 정보에 접근 할 수 있는 간단한 API를 만들어 보겠습니다.

---------------

- IDE : Intellij IDEA  Ultimate 2018.3.5
- Build : Maven

### github 주소
- 계속해서 작성한 전체 소스는 아래 참고해주세요.
    - <https://github.com/JooWon0712/rest-api-with-springboot-kotlin>

------------------

### 시작하기 전에..

포스팅을 시작하기 앞서 수정해야될 부분이 있어 먼저 말씀드리고 시작하겠습니다.

지난번 포스팅에서 작성 했던 **Account** 클래스와 **AccountRepository**의 id 타입을 잘못 선언했었습니다.

- Account
~~~kotlin
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

- AccountRepository

~~~kotlin
@Repository
interface AccountRepository : JpaRepository<Account, Long> {
    fun findByEmail(email: String) : Account?
}
~~~

다시 보시게 되시면 **Account**의 id 타입을 Int로 선언했지만,
**AccountRepositoy**에서 Account의 id 타입을 Long으로 선언했습니다.
저렇게 하게 될 경우 **type casting error**가 발생하게 됩니다.

Account의 ID 또는 AccountRepository의 Account의 ID 타입을 동일하게 맞춰주시면 됩니다.
전 Int로 변경하였습니다.


### 포스팅 시작!

지난번 포스팅에선 Post 메소드까지 만들었습니다.
이번 포스팅에선 생성된 계정 정보 접근(**Get**, **Patch**(생성된 계정 정보중 일부만 변경하기에 Put 대신 Patch로), **Delete**)에 대한 API를 만들어보겠습니다.


### Get 메소드

- AccountController GetMapping 추가
~~~kotlin
@GetMapping("/{id}")
fun getAccount(@PathVariable id: Int) : ResponseEntity<Any> {

    //JpaRepository의 기본 구현체중 하나인 findById()는 return 타입이 optional<T>
    val optionalAccount = accountRepository.findById(id)

    //자바 1.8에서 추가된 optional을 사용하여 null 처리
    //여기서의 null 처리는 id에 해당하는 데이터가 없을 경우
    if (optionalAccount.isEmpty) {
        return ResponseEntity.notFound().build()
    }

    val account = optionalAccount.get()

    val accountResource = AccountResource(account)
    accountResource.add(linkTo(AccountController::class.java).slash(account.id).withSelfRel())
    accountResource.add(linkTo(AccountController::class.java).slash(account.id).withRel("patch-account"))
    accountResource.add(linkTo(AccountController::class.java).slash(account.id).withRel("delete-account"))

    return ResponseEntity.ok(accountResource)
}
~~~

### 테스트 코드 작성
테스트 케이스가 시작되기 전에 실행되는 **@Before** 메소드 생성
~~~kotlin
@Before
fun testSetup() {
    val account = Account(null, "admin@admin.com", "1234")
    accountRepository.save(account)
}
~~~

- getAccount 테스트 코드 : 성공 케이스
~~~kotlin
@Test
fun getAccount() {

    //given
    val id = 1

    //when
    mockMvc.perform(get("/api/accounts/{id}", id)
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .accept(MediaTypes.HAL_JSON))
            .andDo(print())
            //then
            .andExpect(status().isOk)
            .andExpect(jsonPath("id").exists())
            .andExpect(jsonPath("email").exists())
            .andExpect(jsonPath("links[0].rel").value("self"))
}
~~~
- 테스트 결과

![AccountsTests-getAccount]({{ site.baseurl }}/assets/img/AccountsTests-getAccount.png)

- getAccount_fail : 실패 케이스
~~~kotlin
@Test
fun getAccount_fail() {

    //given
    val id = 2

    //when
    mockMvc.perform(get("/api/accounts/{id}", id)
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .accept(MediaTypes.HAL_JSON))
            .andDo(print())
            //then
            .andExpect(status().isNotFound)
}
~~~

- 테스트 결과

![AccountsTests-getAccount_fail]({{ site.baseurl }}/assets/img/AccountsTests-getAccount_fail.png)

### Patch 메소드
- AccountController PatchMapping 추가

~~~kotlin
@PatchMapping("/{id}")
fun updatePasswordForAccount(@PathVariable id: Int, @RequestBody account: Account, errors: Errors): ResponseEntity<Any> {
    if (errors.hasErrors()) {
        return ResponseEntity.badRequest().body(errors)
    }

    val optionalAccount = accountRepository.findById(id)

    if (optionalAccount.isEmpty) {
        return ResponseEntity.notFound().build()
    }

    val getAccount = optionalAccount.get()
    getAccount.password = account.password

    val updateAccount = accountRepository.save(getAccount)

    val accountResource = AccountResource(updateAccount)
    accountResource.add(linkTo(AccountController::class.java).slash(updateAccount.id).withSelfRel())
    accountResource.add(linkTo(AccountController::class.java).slash(updateAccount.id).withRel("patch-account"))
    accountResource.add(linkTo(AccountController::class.java).slash(updateAccount.id).withRel("delete-account"))
    return ResponseEntity.ok(accountResource)
}
~~~

### 테스트 코드 작성
- updatePasswordForAccount : 성공 케이스

~~~kotlin
@Test
fun updatePasswordForAccount() {

    //given
    val accountJson = "{\"email\" : \"admin@admin.com\", \"password\" : \"1234\"}"
    val id = 1

    //when
    mockMvc.perform(patch("/api/accounts/{id}", id)
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .accept(MediaTypes.HAL_JSON)
                .content(accountJson))
            .andDo(print())
            //then
            .andExpect(status().isOk)
}
~~~

- 테스트 결과

![AccountsTests-updatePasswordForAccount]({{ site.baseurl }}/assets/img/AccountsTests-updatePasswordForAccount.png)

**updatePasswordForAccount**의 실패 케이스는 현재 생각해볼만한 케이스는 2가지 입니다.

1.**getAccount_fail**와 동일합니다. 즉 id에 해당하는 Account 정보가 없을 경우 에러가 발생합니다.
그러므로 id의 값을 존재하지 않는 값으로 전송할 경우 실패하게 됩니다.

2.**Account**에서 체크하는 변수에 대한 null 체크 부분입니다.
현재 Account에서 컬럼에 해당하는 변수를 보게 되면 email과 password에 대한 null 체크를 하고 있습니다.
request할때 던져주는 json 데이터중 email이나 password에 해당하는 키값이 없을경우 스프링에서 json parsing중 에러가 발생합니다.

~~~kotlin
@Column(unique = true)
var email: String

@Column(nullable = false)
var password: String
~~~

### Delete 메소드
- AccountController DeleteMapping 추가

~~~kotlin
@DeleteMapping("/{id}")
fun deleteAccount(@PathVariable id: Int) : ResponseEntity<Any> {

    val optionalAccount = accountRepository.findById(id)

    if (optionalAccount.isEmpty) {
        return ResponseEntity.notFound().build()
    }

    val account = optionalAccount.get()
    accountRepository.deleteById(id)

    return ResponseEntity.status(HttpStatus.GONE).body(account)
}
~~~

### 테스트 코드 작성
- deleteAccount : 성공 케이스

~~~kotlin
@Test
fun deleteAccount() {
    //given
    val id = 1

    //when
    mockMvc.perform(delete("/api/accounts/{id}", id)
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .accept(MediaTypes.HAL_JSON))
            .andDo(print())
            //then
            .andExpect(status().isGone)
}
~~~

- 테스트 결과

![AccountsTests-deleteAccount]({{ site.baseurl }}/assets/img/AccountsTests-deleteAccount.png)

### Get 메소드(Pageable)

마지막으로 Account의 모든 계정 정보를 페이징 처리를 하여 가져오는 메소드를 만들어보겠습니다.

- AccountController getMapping 추가
    - 지금 만드는 GetMapping의 경우 **/api/accounts**에 대한 **Get 메소드**입니다.

~~~kotlin
@GetMapping
fun getAccounts(pageable : Pageable, accountPagedResources : PagedResourcesAssembler<Account>) : ResponseEntity<Any> {
    val accounts = accountRepository.findAll(pageable)
    val pagedResource = accountPagedResources.toResource(accounts) {
                val accountResource = AccountResource(it)
                accountResource.add(linkTo(AccountController::class.java).slash(it.id).withSelfRel())
                accountResource}
    return ResponseEntity.ok(pagedResource)
}
~~~

### 테스트 코드 작성

테스트 케이스 생성전 테스트 시작전에 샘플 데이터 생성을 추가로 더 해주셔야됩니다.
전 편의상 복사 붙여넣기 하였습니다.

~~~kotlin
@Before
fun testSetup() {
    val account1 = Account(null, "admin1@admin.com", "1234")
    val account2 = Account(null, "admin2@admin.com", "1234")
    val account3 = Account(null, "admin3@admin.com", "1234")
    val account4 = Account(null, "admin4@admin.com", "1234")
    val account5 = Account(null, "admin5@admin.com", "1234")
    accountRepository.save(account1)
    accountRepository.save(account2)
    accountRepository.save(account3)
    accountRepository.save(account4)
    accountRepository.save(account5)
}
~~~

- getAccounts : 성공 케이스

~~~kotlin
@Test
fun getAccounts() {

    //when
    mockMvc.perform(get("/api/accounts")
                //given
                .param("page", "1")
                .param("size", "2")
                .param("sort", "email,DESC"))
            //then
            .andDo(print())
            .andExpect(status().isOk)
}
~~~

- 테스트 결과

![AccountsTests-getAccounts]({{ site.baseurl }}/assets/img/AccountsTests-getAccounts.png)

- Response Body

![AccountsTests-getAccounts-body]({{ site.baseurl }}/assets/img/AccountsTests-getAccounts-body.png)

**Response Body**를 보면 페이지 링크별 **links** 정보가 넘어온걸 확인 하실수 있습니다.

- **first** : 가장 첫번째 페이지 이동 링크
- **prev** : 이전 페이지 이동 링크
- **self** : 현재 페이지 이동 링크
- **next** : 다음 페이지 이동 링크
- **last** : 가장 마지막 페이지 이동 링크

그 다음 **content**에 배열로 Account의 계정 정보와 해당 계정에 대한 **Self** 링크가 생성되어 있는걸 보실수 있습니다.

그리고 마지막으로 **page**에 페이지 정보가 넘어온걸 확인하실 수 있습니다.

- **size** : 페이징 할 사이즈
- **totalElements** : 총 Account의 갯수
- **totalPages** : 총 페이지 수
- **number** : 현재 페이지 번호

테스트 데이터가 적어 페이지 size를 2로 테스트 하였습니다.
테스트 케이스를 해석하자면..
- 한 페이지에 2개의 Account 정보가 보여지게 될 것입니다.
- 현재 보게될 페이지의 번호는 2번째 페이지 입니다.(**페이지 번호는 0부터 시작**)
- URL에 **page**,**size**, **sort**에 해당 하는 값은 Spring 에서 **Pageable**로 맵핑 해주게 됩니다.


### 번외

아래 링크는 HTTP 응답 코드에 대해서 한번쯤 보시면 좋은 링크 및 동영상입니다.

- HTTP 상태 코드
    - <https://ko.wikipedia.org/wiki/HTTP_%EC%83%81%ED%83%9C_%EC%BD%94%EB%93%9C>
- [동네개발자형] 서버가 요청을 거부했는데 http status 403이 아닌 404로 return 한다고?
    - <https://youtu.be/cE11144NEts>
    
-------------------

간단하게나마 코틀린과 Spring Boot를 이용하여 Restful한 API를 만들어 보는 방법을 포스팅 해보았습니다.
Restful하게 만들어보려고 했지만, 아직 **self-descriptive**에 대한 부분이 빠져있습니다.
이 부분은 현재 작성된 API에 대한 **Rest API Document**를 생성하여 해당 API 프로파일 정보를 넘겨주면 해결할 수 있습니다.

현재 위에 작성된 소스 코드중에서 리팩토링(중복 코드) 해야되는 부분들이 많습니다.
이러한 부분은 혼자서 리팩토링 해보시는 것도 더 도움이 많이 될거라 생각합니다.
또 현재 계정정보에 대한 데이터를 가지고 테스트를 진행하였는데, 계정에 대한 비밀번호가 그대로 노출이 되고 있습니다.
응답 데이터에서 비밀번호에 대한 부분도 처리해야 되는 상황입니다.
부족하게나마 간단한 기능구현에 초점을 맞췄기에 이런 부분은 이해해주시기 바랍니다.