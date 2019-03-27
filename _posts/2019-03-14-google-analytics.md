---
layout: post
title: github 블로그 방문자수 확인
categories: Jekyll
tags: [Jekyll]
comments: true
---

자신의 <code class="highlight">GitHub 블로그</code> 방문자 수를 확인할 수 있는 방법을 포스팅해봅니다.

## Google Analytics
- Google Analytics 가입하기
    -  <https://analytics.google.com/analytics/web/>
        - 계정 이름 : 자신의 계정 이름 입력
        - 웹사이트 이름 : 자신이 블로그 이름 입력
        - 웹사이트 URL : **https://** 선택 
        - 업종 카테고리 : 알맞은 카테고리 선택
        - 보고 시간대 :  **대한민국** 선택

![google-analytics-create]({{ site.baseurl }}/assets/img/google-analytics-create.png)

- UA-ID 확인

![google-analytics-home]({{ site.baseurl }}/assets/img/google-analytics-home.png)
![google-analytics-user-account]({{ site.baseurl }}/assets/img/google-analytics-user-account.png)
![google-analytics-UA-ID]({{ site.baseurl }}/assets/img/google-analytics-UA-ID.png)


- _config.yml 파일 수정    
    - google_analytics 프로퍼티에 자신의 UA-ID 값을 입력한다.
~~~yaml
# Scripts
google_analytics: # Tracking ID, e.g. "UA-000000-01"
disqus_shortname: joowon0712
katex: true # Enable if using math markup
search: true # Enable the search feature
~~~

여기까지 하면 설정이 끝납니다.
**Jekyll Type-Theme**는 기본적인 셋팅이 다 돼있는 상태라 
**_config.yml**에 해당 프로퍼티의 값만 설정 해주면 해당 기능이 활성화 됩니다.

- tracking code가 적용되어 활성화된 모습
![google-analytics-result]({{ site.baseurl }}/assets/img/google-analytics-result.png)