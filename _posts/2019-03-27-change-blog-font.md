---
layout: post
title: github.io 블로그 폰트 변경하기
categories: Jekyll, Google Analytics
tags: [Jekyll, type-theme, Google Fonts]
---

Google Fonts로 github.io 블로그 폰트 변경하기

구글에서 저작권 걱정없이 오픈라이센스로 제공되는 한글폰트는 25개가 있습니다.(**2019.03.27 기준**)

시작하기 앞서 폰트 저작권에 관해서 한번쯤 보시면 좋을 영상과 기사 첨부하였습니다.

- [동네개발자형] 니가 구매한 라이센스가 과연 니 돈을 지켜줄까? 폰트 라이센스 잘찾아보고 써야돼!
    - <https://youtu.be/qRNA13c8-5s?list=PLq7lxfc2HLJCT8h09ttcD3YX6OlXgsEyJ>
- [기사] 구글 무료 한글폰트 25종 제공…저작권 걱정없이 사용하세요
    - <http://www.newsian.co.kr/news/articleView.html?idxno=32584>

---------------

현재 이 블로그는 **Jekyll** 테마중 **type-theme**를 적용하여 블로그를 만들었습니다.

적용 방법은 제 기준으로 적용하는 방법을 포스팅하겠지만, 아마 다른 테마여도 비슷하지 않을까 생각합니다.

- Google Fonts 접속
    - <https://fonts.google.com/>

![google-fonts-com]({{ site.baseurl }}/assets/img/google-fonts-com.png)

- 검색조건 변경
    - Languages : Korean

![google-fonts-com-languages]({{ site.baseurl }}/assets/img/google-fonts-com-languages.png)

위와 같이 검색조건의 언어설정을 Korean 으로 변경하게 되면 현재 오픈라이센스로 제공되는 무료 하늘 폰트를 보실 수 있습니다.

저는 **Noto Sans KR** 폰트를 적용하겠습니다.

**Noto Sans KR** 오른쪽에 플러스 버튼을 클릭합니다.

![google-fonts-com-Noto-Sans-KR-check]({{ site.baseurl }}/assets/img/google-fonts-com-Noto-Sans-KR-check.png)

그러면 아래와 같이 **1 Family Selected**라는 팝업창이 생깁니다.
그 파업 창을 클릭합니다. 

![google-fonts-com-Noto-Sans-KR-select]({{ site.baseurl }}/assets/img/google-fonts-com-font-Noto-Sans-KR-select.png)

해당 폰트를 적용하는 방법이 나옵니다.

![google-fonts-com-Noto-Sans-KR-info]({{ site.baseurl }}/assets/img/google-fonts-com-Noto-Sans-KR-info.png)

**CUSTOMIZE** 버튼을 클릭해보시면 폰트의 스타일 및 지원 Language, 폰트 로딩 타임을 보실 수 있습니다.

![google-fonts-com-Noto-Sans-KR-customize]({{ site.baseurl }}/assets/img/google-fonts-com-Noto-Sans-KR-customize.png)

- 추가한 옵션
    - medium 500
    - Languages : Korean

EMBED 탭을 클릭하게 되면 아래와 같이 **STANDARD**가 변경된걸 보실 수 있습니다.    

![google-fonts-com-Noto-Sans-KR-custom-info]({{ site.baseurl }}/assets/img/google-fonts-com-Noto-Sans-KR-custom-info.png)

### 적용하기

적용 방법은 매우 간단합니다.

- _config.yml 수정
~~~yml
google_fonts: "Noto+Sans+KR:400,500"
~~~

- _variables.scss 수정

**_variables.scss** 파일 경로는 _sass/base 디렉토리 안에 있습니다.

~~~scss
$font-family-main: 'Noto Sans KR', sans-serif;
$font-family-headings: 'Noto Sans KR', sans-serif;
~~~

로컬에서 먼저 확인하시는 방법은 Jykell 서버를 재시작하시면 되고, 블로그에 적용하는 방법은 git push를 하시면 됩니다.