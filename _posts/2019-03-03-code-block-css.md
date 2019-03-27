---
layout: post
title: MarkDown 코드 블럭 스타일 변경
categories: Jekyll
tags: [Jekyll, MarkDown, type-theme]
comments: true
---

<code class="highlight">Jekyll</code>테마중 <code class="highlight">type-theme</code>를 이용하여 블로그를 만들었습니다. 블로그를 만들고 코드블럭에 소스 코드를 붙여 넣었는데 블로그 배경색과 코드 블랙 배경색이 동일하여 가독성이 떨어져보였습니다.
간단하게나마 코드블럭 배경색을 변경하는 방법을 알아보았습니다.
(다른 테마를 이용하여 설치하셨다 해도 적용 방법은 비슷할거라 생각됩니다.)

현재 <code class="highlight">type-theme</code>의 코드 블럭 배경색은 아래와 같습니다.

![BEFORE]({{ site.baseurl }}/assets/img/code-block-before.png)

<code class="highlight">_config.yml</code> 열어봅니다.

~~~

highlighter: rouge

~~~

rouge를 이용하면 코드 블럭의 syntax highlighting 기능을 이용할 수 있습니다.
해당 블로그를 셋팅할때 rouge를 따로 설치하지 않았기 때문에 테마에 설정된 highlighting 스타일로 적용된 상태입니다.
이 설정을 바꾸려면 rouge를 설치해야 합니다.

~~~

 > gem install rouge

~~~

rouge 설치가 완료되면 다음 명령어로 설치할 css 목록을 확인해봅니다.

~~~

 > rougify help style


usage: rougify style [<theme-name>] [<options>]

Print CSS styles for the given theme.  Extra options are
passed to the theme. To select a mode (light/dark) for the
theme, append '.light' or '.dark' to the <theme-name>
respectively. Theme defaults to thankful_eyes.

options:
  --scope	(default: .highlight) a css selector to scope by

available themes:
  base16, base16.dark, base16.light, base16.monokai, base16.monokai.dark, base16.monokai.light, base16.solarized, base16.solarized.dark, base16.solarized.light, colorful, github, gruvbox, gruvbox.dark, gruvbox.light, igorpro, molokai, monokai, monokai.sublime, pastie, thankful_eyes, tulip
  
~~~

블로그 작성 기준으로 유요한 테마는 아래와 같습니다.**(2019-03-03 기준)**

- base16
- base16.dark
- base16.light
- base16.monokai
- base16.monokai.dark
- base16.monokai.light
- base16.solarized
- base16.solarized.dark
- base16.solarized.light
- colorful
- github
- gruvbox
- gruvbox.dark
- gruvbox.light
- igorpro
- molokai
- monokai
- monokai.sublime
- pastie
- thankful_eyes
- tulip

저의 경우 <code class="highlight">base16.monokai.dark</code>를 설치하였습니다.

rouge style 다운로드
~~~

 > rougify style base16.monokai.dark > assets/css/syntax.css

~~~

저같은 경우 위 명령어를 프로젝트 디렉토리에서 입력했기 때문에 프로젝트 디렉토리에 <code class="highlight">assets/css/syntax.css</code>라는 파일이 생겼습니다.

그리고 <code class="highlight">_includes/head.html</code> 파일을 열어 아래 내용을 추가합니다.

~~~html
<!-- syntax.css -->
<link rel="stylesheet" href="/assets/css/syntax.css">
~~~

여기까지 하셨다면 아래와 같이 스타일이 적용된걸 확인 하실수 있습니다.

~~~java
public class JooWonWorld {
    public static void main(String[] args) {
        System.out.println("Hello JooWon World!");
    }
}
~~~

만약 적용한 스타일이 맘에 들지 않는다면 다른 스타일을 다운받아 적용하여 맘에 드시는 스타일을 적용하시길 바랍니다.

