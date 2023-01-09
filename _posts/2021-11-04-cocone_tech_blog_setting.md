---
layout: post
title: "Cocone M TechUp Blog 구축"
excerpt: "git blog를 사용하여 기술 블로그 구축과 포스팅을 해보자!"
author: you_areum
comments: true
categories: Culture
date: 2021-11-04
image: https://user-images.githubusercontent.com/93178834/139623172-2ea6dde4-6b58-403f-b053-f14a2b30e3d6.png
thumbnail: https://user-images.githubusercontent.com/93178834/139623172-2ea6dde4-6b58-403f-b053-f14a2b30e3d6.png
tags: [jekyll, markdown, gitblog, github, bundle]
---

안녕하세요😊<br>
깃 블로그를 활용하여 코코네 기술 블로그를 작성해보려 합니다.<br>
아직 환경에 익숙하지 않아서 많이 어설프겠지만, 앞으로 크고 작은 지식들을 공유하며 기술 블로그를 활성화 시켰으면 좋겠습니다!<br>
먼저 블로그 포스팅을 하기 위한 초기 환경 구성 방법을 알아보겠습니다.<br>

<!-- 1. [초기 세팅](#1.-초기-세팅)
2. [블로그 포스팅 방법](#2.-블로그-포스팅-방법)
3. [후기](#3.-후기) -->

### 환경 설정

깃 블로그를 사용하기 위해 먼저 해당 respository를 로컬 환경으로 clone을 해와야 합니다.<br><br>
![repo clone](https://user-images.githubusercontent.com/93178834/139623172-2ea6dde4-6b58-403f-b053-f14a2b30e3d6.png)<br>

clone 후 디렉토리 구조를 간단히 살펴보겠습니다.

```
_data : 사이트를 생성할 때 사용하는 부가적인 데이터를 저장하는 공간
_include : 재사용을 위해서 파일을 담는 공간
_layouts : 포스트나 페이지를 포장할 때 사용하는 템플릿
_posts : 작성한 포스트가 저장되는 폴더
_sass : CSS 퍼블 관련 소스들
_config.yml : 사이트의 환경설정을 작성할 수 있는 파일
```

<br>
이제 터미널을 열어 로컬에 jekyll과 ruby를 설치해 보겠습니다.
```
$ gem install jekyll
$ brew install ruby
```
<br>
<!-- Jekyll은 정적인 웹사이트 생성기로 Ruby라는 언어를 기반으로 제작되었고 마크다운 방식으로 글쓰기가 가능한 도구 입니다. -->

설치를 완료 했으면 이제 Jekyll 페이지를 생성해 주겠습니다.

```javascir
$ jekyll new [github_name].github.io
  (ex. jekyll new cocone.github.io)
```

패이지 설치가 완료 되었다면 패키지와 Jekyll을 로컬 서버에 띄워줍니다.

```
$ bundle install
$ jekyll serve
```

![server](https://user-images.githubusercontent.com/93178834/139631549-db5201b5-4afb-414b-9818-66dc32be00dd.png)<br>

주소를 확인 후, 브라우저 주소창에 입력하면 아래와 같이 로컬에서도 확인이 가능합니다.<br><br>
![server](https://user-images.githubusercontent.com/93178834/140276478-726367b8-fdb8-4081-976b-ade811396523.png)<br>

---

이제 포스팅 방법에 대해 알아보겠습니다.

### 블로그 포스팅 방법

포스팅은 \_posts 폴더 내부에 markdown(.md, .markdown)이나 html 형식으로 작성해야 합니다.<br>
마크다운(markdown)은 일반 텍스트 기반의 마크업 언어로 문법이 간결하고 쉬우며 html로도 변환이 가능합니다.<br>
저는 마크다운 형식을 사용해서 포스팅을 해보려고 합니다!<br>
먼저, 파일명은 yyyy-mm-dd-포스트제목.md 형식으로 작성해야 합니다.<br>
(날짜가 꼭 당일이 아니어도 되지만, 미래는 불가 합니다.)<br>
`ex) 2021-11-04-hello_world.md`

포스트 작성시에는 상단에 key:value 형식을 사용하여 아래와 같이 메타정보를 적어줍니다.

```
layout: post
title:  "cocone posting"   // 포스트 제목
comments: true   // 댓글 허용 여부
excerpt: "cocone 첫 Tech blog를 작성해보자!" // 포스팅 미리보기 글
date:   2021-11-04 // 글 작성일
tags: [jekyll, bundle, markdown, ruby]   // 포스트에 달 태그 작성
```

<br>
상단에 메타 정보들까지 작성했으면 하단에 markdown 문법에 맞춰 글 작성 해줍니다.<br>
그리고 _post 폴더 내부에 파일 작성 후, git push를 해줍니다.<br>
이제 http://[github_name].github.io 접속하면 방금 적은 포스팅을 확인 할 수 있습니다!!😇<br><br>

![server](https://user-images.githubusercontent.com/93178834/140290444-d09e323c-498e-4f1a-8011-b24714720db8.png)<br>

### 후기

첫 포스팅이라 두서없이 글을 적은거 같아서 걱정되지만 앞으로 코코네의 여러가지 서비스들이나, 개발하면서 생기는 여러가지 궁금증이나 문제들에 대해서도 포스팅하며 함께 성장해 나가면 좋겠습니다.
이상으로 코코네 테크 블로그 구축기 및 첫 포스팅을 마치도록 하겠습니다!🧡

---
