---
layout: post
title: "Cocone M Tech Blog 소개 & 포스팅 가이드"
excerpt: ""
author: han_jaesun
comments: true
categories: Culture
date: 2023-01-15
image: https://user-images.githubusercontent.com/93178834/139623172-2ea6dde4-6b58-403f-b053-f14a2b30e3d6.png
thumbnail: https://user-images.githubusercontent.com/93178834/139623172-2ea6dde4-6b58-403f-b053-f14a2b30e3d6.png
tags: [techblog, guide, markdown]
---

## 안녕하세요😊

2022년 맞이한지가 엊그제 같은데, 벌써 2023년 새해가 밝았네요~ 🌄

모두 2023년도 새해복 많이 받으시고, 하시고자 하는 일들도 술술 풀리는 행복한 한 해 되셨으면 좋겠습니다~!

자~ 그럼 오늘은! 어떤 블로그 포스트를 가지고 왔냐면요!
**Cocone M Tech Blog 소개 & 포스팅 가이드**입니다!

Cocone M에는 크루들의 역량 강화를 위한 테크업 데이, 테크 블로그, 플레이샵 등 여러 문화가 있는데요, 이번 포스트는 테크 블로그와 관련된 내용으로 구성해봤습니다~!

Cocone M에서는 더 **건강한 개발 문화** 를 만들기 위해 매년 새로운 시도를 하고 있고, 이 새로운 시도들 사이에서 크루들이 접한 기술, 신기술, 개발 노하우 등을 공유하고 알리기 위한 목적으로 테크 블로그를 개설했습니다!

그래서 2023년부터는 크루들이 이 테크 블로그를 좀 더 적극적으로 활용할 수 있도록, 몇 가지를 계획하고 있습니다~!

2023년에는 **CocoJam** 이라는 프로젝트 개발 대회를 진행하고, 이 CocoJam에 참여한 크루들이 접한 기술이나 개발 기록 등의 내용들을 정리해서 블로그에 공유하고자 합니다~! ( 추후에는 CocoJam이 어떤 문화인지에 대한 소개글을 올리면 좋을 것 같네요! )

또한 기술 이야기 외에도 Cocone M의 크루들이 보내는 일상도 블로그에 업로드 될 예정이니, 많은 관심 부탁드려요!

<br/>
<br/>

---

## **0. Cocone M Tech 블로그 소개**

블로그 포스팅 방법을 얘기하기 전에, 저희 테크 블로그에 사용한 기술에 대해 간단히 소개부터 할게요!

Cocone M 테크 블로그는 [Github pages]와 [Jekyll]을 활용하여 제작되었습니다.([jekyll 한국어 버전])

Github Pages는 Github에서 제공하는 정적 웹페이지 호스팅 서비스로, Github에서 생성한 Repository를 직접 호스팅 할 수 있습니다.

여기에 Jekyll(지킬) 이라는 정적 웹페이지 생성기(SSG : Static Site Generator)를 사용해서 쉽게 페이지를 작성할 수 있도록 만들었습니다. 이 Jekyll은 Markdown과 HTML로 작성된 파일에, 선택한 레이아웃을 입혀서 웹 사이트를 생성해주는 유용한 생성기입니다.

Github Pages 와 Jekyll을 활용하여 블로그를 구축하는 방법에 대해서는, 테크 블로그 첫 번째 포스트인 **[Cocone M TechUp Blog 구축]** 포스트에 상세히 적혀있습니다~! 관심있으신 분들은 해당 포스트를 참고해주시면 좋을 것 같아요!

[github pages]: https://pages.github.com/ "Github Pages - Websites for you and your projects."
[jekyll]: https://jekyllrb.com/ "Jekyll 공식 사이트"
[jekyll 한국어 버전]: https://jekyllrb-ko.github.io/ "Jekyll 한국어 공식 사이트"
[cocone m techup blog 구축]: http://techup.cocone.co.kr/culture/2021/11/04/cocone_tech_blog_setting.html "Cocone M TechUp Blog 구축"

<br/>
<br/>

---

## 1. 환경 설정

저희 블로그에 올라가는 포스트는 모두 마크다운 파일입니다.
마크다운 파일을 작성할 때, 텍스트 편집기에서 작성해도 되지만, 좀 편하게 작성하고 싶다면 마크다운 에디터를 활용하는 것도 좋은 방법이죠!

구글에 마크다운 에디터를 검색하면 몇 가지가 나오는데요~ 이 중에 저는 VS Code 의 확장 프로그램 **[Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)** 을 사용해봤습니다.

|                                                                                    텍스트 편집기 사용                                                                                    |                                                                                    VS Code Markdown All in One                                                                                    |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| <img width="698" alt="마크다운파일 텍스트 편집기 버전" src="https://user-images.githubusercontent.com/122719990/212536374-3974f164-28b0-4867-8f4d-d0f84de9e81d.png"> | <img width="1290" alt="마크다운 vs code 확장프로그램 사용 버전" src="https://user-images.githubusercontent.com/122719990/212536747-72921873-3b65-4fd7-a9e5-048f5a3a86bf.png"> |
|                                   [원본이미지](https://user-images.githubusercontent.com/122719990/212536374-3974f164-28b0-4867-8f4d-d0f84de9e81d.png)                                   |                                       [원본이미지](https://user-images.githubusercontent.com/122719990/212536747-72921873-3b65-4fd7-a9e5-048f5a3a86bf.png)                                        |

마크다운 전용 에디터들은 대부분 **미리보기** 기능을 제공하는 것 같더라고요! 이 부분이 전용 에디터를 사용하는 가장 큰 장점으로 느껴졌답니다~! 이것 외에도 다양한 마크다운 에디터가 있는 듯 하니, 관심있으신 분들은 한 번 찾아보시는 것도 좋을 것 같네요!

다시 본론으로 돌아가서, Markdown All in One 을 설치해볼게요! 아주 간단합니다.

1. VS Code 를 연다([없으신분들은 설치 ㄱㄱ](https://code.visualstudio.com/))
2. 왼쪽 탭 중 확장을 선택한다
3. 검색란에 markdown all in one 을 검색하고 해당 프로그램을 선택한다.
4. 설치를 누른다.
   <img width="852" alt="마크다운 확장 프로그램 추가" src="https://user-images.githubusercontent.com/122719990/212537388-852b5e55-c20e-418e-aa32-79bf199a2857.png">

끗! ㅇㅅㅇb
<br/>
<br/>

---

## 2. 포스트글 작성하기

### - 포스트글 파일 생성 ( MD 파일 생성 )

- VS Code 에서 새 파일을 생성합니다.

    <img width="350" alt="마크다운 새파일 클릭" src="https://user-images.githubusercontent.com/122719990/212537645-70feff41-def8-4856-8981-e549c8b06c2f.png">

  새 파일을 저장합니다.

  ### **저장할 때 주의할 점!!!!**

  ```
  파일명 형식 : yyyy-mm-dd-포스트제목.md
  주의 : 날짜가 꼭 당일이 아니어도 되지만, 미래는 불가 합니다.
  예시 : 2023-01-15-cocone_tech_blog_guide.md
  ```

    <img width="756" alt="마크다운 파일 저장 이름 지정" src="https://user-images.githubusercontent.com/122719990/212537754-e4eaf4e1-4bae-4846-a5a7-50c27e7a168b.png">

### - 내용 작성

- 글을 작성할 때는, 반드시 상단에 메타 정보를 작성해주세요! 작성한 메타 정보들은 블로그 리스트의 포스트 카드에 표시되는 내용들입니다~! (이 메타 정보는 추후 변경될 수 있습니다. 변경될때 이 포스트글도 수정해서 업데이트 할게요!)

      layout: post
      title:  "포스트 제목"
      comments: true   // 댓글 허용 여부
      excerpt: "포스팅 미리보기 글"
      date:   2023-01-15 // 글 작성일
      tags: [post, tag, example, markdown] // 포스트에 달 태그

  아래 이미지가 그 예시입니다~!

  <img width="699" alt="마크다운 메타 데이터 예시" src="https://user-images.githubusercontent.com/122719990/212538480-4374cccb-d8cb-4300-acb3-d6b780567096.png">

- 이제 마크다운 문법에 맞춰 내용을 자유롭게 작성해주시면 됩니다~!
  마크다운의 모든 문법까지 이 포스트 글에서 다루기는 어려울 것 같아서, 자주 쓰이는 것들 위주로 몇 가지만 소개할게요~!

<br/>
<br/>

---

## 3. 마크다운 문법

- 헤더 설정

  특수문자 \# 을 이용해 헤더를 설정

  # \# 헤더 1

  ## \## 헤더 2

  ### \### 헤더 3

  #### \#### 헤더 4

  ##### \##### 헤더 5

  ###### \###### 헤더 6

  <br/>

- 코드박스

  ```
  ` 를 3번 이상 입력하고 코드 종류도 적습니다.
  ```

  아래는 사용 예시입니다.

  <img width="385" alt="마크다운 코드 박스 예시" src="https://user-images.githubusercontent.com/122719990/212541254-ef0fd0de-f48a-4f30-8878-6091475c28cc.png">

  이렇게 적으면 아래와 같은 코드박스가 보이게 되죠.

  ```html
  <h1>` 를 3번 이상 입력하고 코드 종류도 적습니다.</h1>
  <a href="https://www.naver.com/">네이버</a>
  ```

- **텍스트 강조 (두꺼운 글씨)**

  텍스트 강조는 여러 방법이 있는데 저는 **별 두 개(\*\*)** 를 주로 사용합니다.

  ```
  **(강조할 내용)**
  ```

  결과

  **(강조함)**

- 이미지 추가

  이미지는 아래와 같은 양식으로 적으면 됩니다.

  ```md
  ![CoconeMLogo](https://www.cocone-m.com/_next/image?url=%2Fcommon%2Flogo.png&w=384&q=75)
  ```

  괄호 안에 이미지 링크를 넣으면 해당 이미지가 아래처럼 뜰겁니다.

  ![CoconeMLogo](https://www.cocone-m.com/_next/image?url=%2Fcommon%2Flogo.png&w=384&q=75)

- 외부 링크 첨부

  링크를 첨부하는 방법은 여러가지가 있습니다.

  ```md
  - 인라인 링크
    [네이버](http://naver.com "링크 제목. 마우스를 링크에 가져다 댔을 때 떠는 문구입니다.")

  - 참조 링크

    [구글][참조 링크]

    문서 안에서 [참조 링크]를 그대로 적을 수 있습니다.

    [참조 링크]: https://www.google.com/ "구글페이지로 이동합니다"

  - URL 링크 <https://www.google.com//>
  ```

  아래는 실행결과 입니다

  - 인라인 링크
    [네이버](http://naver.com "링크 제목. 마우스를 링크에 가져다 댔을 때 떠는 문구입니다.")

  - 참조 링크

    [구글][참조 링크]

    문서 안에서 [참조 링크]를 그대로 적을 수 있습니다.

    [참조 링크]: https://www.google.com/ "구글페이지로 이동합니다"

  - URL 링크 <https://www.google.com//>

자주 쓰이는 것들은 대략 이정도 인것 같네요~!

 <br/>
 <br/>

---

## 마치며...

처음 포스트를 쓸 때는 어떻게 써야 할지 잘 몰라서 다른 크루분들께 여쭤도 보고, 자료도 찾아가며 썼던 기억이 있는데요~ 이 글이 포스트를 작성할 다른 크루분들께 조금이나마 도움이 되었으면 하는 바람입니다~!

그럼 오늘도 모두 좋은 하루 되세요~! 🍀

<img src="https://user-images.githubusercontent.com/17717157/177521177-d114fd9d-e368-4a72-9d44-fdbc2957a205.png" width="50" height="50"/>
