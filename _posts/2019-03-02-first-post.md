---
layout: post
title: github.io 블로그 만들기
categories: Jekyll
tags: [Jekyll, type-theme]
comments: true
---

<code class="highlight">Jekyll</code>테마중 <code class="highlight">type-theme</code>를 이용하여 블로그를 만들어보려고 합니다.
제가 작업한 환경은 맥북에서 작업한 내용이므로 윈도우에서는 다른 블로그를 참고해주시면 감사하겠습니다.

## Jekyll이란

Jekyll은 Ruby 기반의 아주 심플하고 블로그 지향적인 정적 사이트 생성기입니다.

텍스트(마크다운) 파일로 컨텐츠를 작성하고, 폴더로 그 파일들을 정리합니다. 다음, Liquid 기능이 추가된 HTML 템플릿을 사용해 사이트의 모양을 만듭니다. Jekyll 은 자동으로 내용물과 템플릿들을 함께 합쳐서, 어떤 서버에서도 올바르게 작동하는, 완전한 정적 웹사이트를 생성합니다.

Jekyll 은 **GitHub Pages** 의 **내부 엔진**이기 때문에, 당신의 프로젝트에 있는 Jekyll 페이지/블로그/웹사이트를 GitHub 서버에 **무료**로 **호스팅** 할 수 있습니다.

자세한 내용은 아래 링크 참조해주세요.
- <https://jekyllrb-ko.github.io/>

## GitHub에 블로그용 저장소 만들기
- 다른 설정값들은 디볼트 설정값
- 저장소 이름 **{githubID}.github.io** 생성
- ex : 제 github 아이디는 joowon0712 이기 때문에 **joowon0712.github.io**로 설정

## 생성한 저장소 Clone 받기
- 전 git Command가 아직 익숙하지 않아 Sourcetree를 이용하였습니다.

## 테마 선택하기
- 아래 사이트에서 맘에 드는 테마를 선택합니다.
- <http://jekyllthemes.org/>
- 전 10번째 페이지에 있는 <code class="highlight">type-theme</code>를 선택하였습니다.
- **Download** 버튼을 눌러 소스를 다운 받습니다.
- **Demo** 버튼을 눌러 미리보기 할 수 있습니다.
![type-theme-info]({{ site.baseurl }}/assets/img/type-theme-info.png)

## 테마 설치하기
- 압축 해제 후 생성된 소스 파일을 방금전 Clone 받은 디렉토리에 복사합니다.
 
![type-theme-source]({{ site.baseurl }}/assets/img/type-theme-source.png)

## _config.yml 수정
- _config.yml 파일을 열어 다음부분을 수정합니다.

**BEFORE**
~~~
# SITE CONFIGURATION
baseurl: "/type-theme"
url: "https://rohanchandra.github.io/"
~~~

**AFTER**
~~~
# SITE CONFIGURATION
url: "https://joowon0712.github.io"
baseurl: ""
~~~

## .gitignore
- 저는 IntelliJ IDEA를 사용해서 IDE 관련 설정도 추가했습니다.

~~~
### IntelliJ IDEA ###
.idea
*.iws
*.iml
*.ipr

*.DS_Store
*.gem
.bundle
.sass-cache
_site
Gemfile.lock
~~~

## git Push
- 여기까지 하고 자신의 저장소에 commit 후 push 하면 자신의 블로그가 생성된걸 확인 할 수 있습니다.

## 로컬 환경 설정
- 위 작업까진 그냥 github 블로그를 띄워보기만한 방법이었습니다.
- 자신에게 맞게 커스터마이징을 하려면 로컬 환경 설정 후 로컬에서 다양한 방법으로 작업 후 저장소에 반영해야합니다.
- 다양한 블로그를 찾아보며 따라해봤는데 제게 가장 참고하였던 블로그 링크를 남기겠습니다.
    - <https://lhy.kr/create-jekyll-blog-using-rbenv-and-github-pages>
- **type-theme**설치 및 로컬 환경 설정 후 로컬에서 jekyll 서버를 실행할때 **bundler** 버전 오류가 발생할 경우 **Gemfile.lock** 파일에서 bundler 버전을 로컬에 설치된 bundler 버전에 맞춰주시면 됩니다. 