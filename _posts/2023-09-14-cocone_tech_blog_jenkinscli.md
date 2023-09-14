---
layout: post
title: "Jenkins CLI 활용하기"
comments: true
excerpt: ""
date: 2023-09-14
tags: [jenkins]
thumbnail: https://github.com/hanjaesun0412/TechBlog/assets/126373491/d65e454e-af94-4bd1-9b64-153609bb52a8
---

# Jenkins CLI 활용하기

---

![Untitled](https://github.com/hanjaesun0412/TechBlog/assets/126373491/d65e454e-af94-4bd1-9b64-153609bb52a8)
안녕하세요, MLD 컴퍼니의 클라이언트 개발자 입니다.

MLD컴퍼니에서는 Jenkins에서 다양한 잡들을 만들어 사용하고는 하는데요.

이번 글에서는 Jenkins를 CLI로 Jenkins를 더 혹사시킬 수 방법을 공유드리려합니다.

## **[SSH Public Key 등록]**

---

jenkins user 설정 에서 ssh key 등록을 하셔야 합니다.

![Untitled](https://github.com/hanjaesun0412/TechBlog/assets/126373491/5c20587f-82d6-494a-9cec-936e3cf0dc9d)

![Untitled](https://github.com/hanjaesun0412/TechBlog/assets/126373491/8852ec36-bd38-45d4-a07d-3ff175e6bf6d)

## [Jenkins CLI 설치]

---

### Jenkins주소 뒤에 cli입력

- 예: http://jenkins.cocone-m.kr:1234/ → http://jenkins.cocone-m.kr:1234/cli

![Untitled](https://github.com/hanjaesun0412/TechBlog/assets/126373491/2111da75-3651-4e92-ade1-a1097704c553)

### jenkins-cli.jar파일 다운로드

![Untitled](https://github.com/hanjaesun0412/TechBlog/assets/126373491/713d39f8-ebb6-4fd4-9b40-88284911d1eb)

## [사용가능한 명령어들]

---

아래의 스크린샷 외에도 많은 명령어들이 있으나

지금은 `who-am-i` 와 `build`명령어만 공유드리겠습니다.

![Comands](https://github.com/hanjaesun0412/TechBlog/assets/126373491/471f081f-d547-48ac-868e-2cb7624d82ec)

## [사용법]

---

아래처럼 각각에 맞는 값을 입력해서 진행하시면 문제없이 작동합니다.

```python
java -jar jenkins-cli.jar -s http://<jenkins_server>:<port>/ <command>
```

예를들어

- jenkins_server : jenkins.cocone-m.kr
- port : 1234
- command : who-am-i

```python
java -jar jenkins-cli.jar -s http://jenkins.cocone-m.kr:1234/who-am-i
```

![Untitled](https://github.com/hanjaesun0412/TechBlog/assets/126373491/8356a12a-6134-4fd0-800f-ff1b0cce4f8a)

위처럼 제대로 실행되는것을 볼수있습니다.

이제 다른 젠킨스 잡들을 실행시키기 위해 build 커맨드를 사용하기에 앞써 해당 커맨드에는

아래의 옵션이 있습니다.

```python
-c  : Check for SCM changes before starting the build, and if there's no
       change, exit without doing a build (default: false)
 -f  : Follow the build progress. Like -s only interrupts are not passed
       through to the build. (default: false)
 -p  : Specify the build parameters in the key=value format. (default: {})
 -s  : Wait until the completion/abortion of the command. Interrupts are passed
       through to the build. (default: false)
 -v  : Prints out the console output of the build. Use with -s (default: false)
 -w  : Wait until the start of the command (default: false)
```

위에서 -s -v -p 만 사용하겠습니다.

- -s **`명령이 완료/중단될 때까지 기다립니다.`**
- -v **`빌드의 콘솔 출력을 인쇄합니다. -s와 함께 사용`**
- -p **`key=value 형식으로 빌드 매개변수를 지정합니다. (기본값: {})`**
  - Test_Job에는 V_VERSION이라는 변수를 넣겠습니다.

```python
java -jar jenkins-cli.jar -s http://jenkins.cocone-m.kr:1234/ build Test_Job -s -v -p V_VERSION=9999
```

아래처럼 로그를 통해 젠킨스 실행이 되는 것을 알수있습니다.

![Untitled](https://github.com/hanjaesun0412/TechBlog/assets/126373491/d65e454e-af94-4bd1-9b64-153609bb52a8)

## [이후에 활용 TIP]

---

- MLD 컴퍼니에서는 `boto3`, `gspread` \*\*모듈을 사용해 AWS S3와 Google Sheet에서 리소스를 가져와 업로드하는 작업들을 자동화를 진행하고 있습니다.
- 또한 계속 업데이트가 필요한 작업들은 배치파일을 통해 실행하면 가능합니다.
