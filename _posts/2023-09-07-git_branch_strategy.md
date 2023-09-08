---
layout: post
title: "Git Branch 전략에 대해 알아보자"
comments: true
excerpt: "Git Branch 전략에 대해 알아보자"
date: 2023-09-07
tags: [Git]
thumbnail: https://s3.ap-northeast-1.amazonaws.com/tane.coconeimg.jp/img/git.png
---

안녕하세요 👋

CoconeM **마이리틀돌 개발팀**입니다.

오늘은 마이리틀돌 개발팀에서 사용하고 있는 **Git Branch 전략**에 대해 소개해보려고 합니다. 😎

마이리틀돌 앱 어플리케이션 서버팀에서는 어떤 Git Branch 전략을 사용하고 있는지 참고용으로 봐주시면 감사하겠습니다.

먼저 Git Branch 전략 중 대표적인 Git Flow 와 Github Flow, GitLab Flow에 대해 살펴봅시다.

<br>

## Git Flow 란?

먼저 Core Branch 인 master, develop Branch 가 존재합니다.

그리고 Supporting Branch 로 Feature, Release, Hotfix Branch 를 둡니다.

#### Core Branch

- master Branch : 출시 가능 코드를 모아두는 항상 production-ready 상태인 Branch

- develop Branch : 다음 버전 개발 코드를 모아두는 Branch

#### Supporting Branch

- Feature Branch : 기능 개발 branch. origin에 있으면 안되고 개발자 repo에 존재해야 함. develop Branch에서 분기되고 develop Branch로 병합. Fast-Foward로 병합하지 않고 commit history를 남기며 병합 권장.

- Release Branch : 다음 버전 출시를 위한 Branch. 버전 정보 수정, 사소한 bug-fix를 진행. develop Branch에서 분기하여 develop, master Branch에 병합.

- Hotfix Branch : 이미 출시된 버전의 오류 수정을 위한 Branch. master Branch에서 분기하여 develop, master Branch에 병합.

<br>

Git Flow 전략은 다음 이미지로 정확하게 확인이 가능합니다.

<br>

<p align="center"><img src="https://s3.ap-northeast-1.amazonaws.com/tane.coconeimg.jp/img/git-flow.png" width="500px"></p>

<br>

전체적인 Git Flow 전략은 다음과 같이 진행됩니다.

먼저 위의 이미지와 같이 master Branch가 존재하고 master Branch에서 develop Branch를 분기하였습니다.

그리고 develop Branch에서 feature branch들을 분기하여 기능 개발을 하여 다시 develop Branch에 merge 합니다.

이후 출시 예정일이 잡히면 develop Branch에서 release Branch를 분기하고 버전 정보 수정, bug-fix를 진행하고 develop, master Branch에 merge 합니다.

차후 출시된 버전에 오류가 있으면 master Branch에서 hotfix Branch를 분기하여 버그를 수정하고 develop, master Branch에 merge 합니다.

좀 더 자세한 내용은 Vincent Driessen이 2010년 제안한 [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/) 에서 확인이 가능합니다.

<br>

---

<br>
<br>

다음으로는 Github Flow 전략에 대해 알아보겠습니다.

<br>

## Github Flow 란?

- master Branch : merge와 동시에 배포되도록 CI/CD가 설정되는 Branch

- Branch : master Branch에서 분기된 Branch. 기능 개발, 버그 수정 등의 모든 작업 진행.

Github Flow는 Git Flow에서 develop, release Branch를 제거하고 hotfix, feature Branch를 하나로 합친 Flow라고 볼 수 있습니다.

master Branch에서 기능 개발, 버그 수정이 필요한 경우 Branch를 하나 분기하여 작업한 뒤 다시 master Branch에 merge 하는 방식입니다.

이 때 master Branch에 merge가 되는 동시에 즉시 배포가 이루어집니다.

논의를 통해 브랜치에 대한 강력한 rule을 설정해야합니다.(branch 명, commit message 등)

Git Flow와는 다르게 분기한 Branch는 수시로 원격에 push하여 업데이트합니다.

merge와 동시에 배포가 이뤄지므로 master Branch에 merge 하기 전에는 PR(Pull Request)를 올리고 리뷰를 받습니다.

좀 더 자세한 내용은 Github Docs [GitHub 흐름](https://docs.github.com/ko/get-started/quickstart/github-flow) 에서 확인이 가능합니다.
<br>

---

<br>
<br>

마지막으로 GitLab Flow 전략에 대해 알아보겠습니다.

<br>

## GitLab Flow 란?

Gitlab Flow에는 master, production, pre-production, feature Branch가 존재합니다.

앞의 전략들과 Branch 명이 헷갈릴 수 있으니 주의가 필요합니다.

- master Branch : Git Flow의 develop Branch와 동일 역할

- Feature Branch : master Branch에서 분기된 Branch. 기능 개발, 버그 수정 등의 모든 작업 진행

- pre-production Branch : master Branch에서 production Branch로의 merge 이전에 test server Branch 용도로 사용.

- production Branch : Git Flow의 master Branch와 동일 역할

<br>

GitLab Flow 전략은 다음 이미지로 확인이 가능합니다.

<br>

<p align="center"><img src="https://s3.ap-northeast-1.amazonaws.com/tane.coconeimg.jp/img/gitlab-flow.png" width="300px"></p>

<br>

먼저 다른 전략들과 마찬가지로 master Branch가 있고 master Branch에서 feature Branch를 분기하여 작업한 뒤 다시 master Branch에 merge 합니다.

이 때 master Branch는 Git Flow 전략의 develop Branch와 유사하다고 보시면 됩니다.

master Branch에서 production Branch로 바로 merge 하는 경우도 있고 그 사이에 pre-production Branch를 두어 따로 test server를 관리하는 경우도 있습니다.

좀 더 자세한 내용은 GitLab Blog [Combine GitLab Flow and GitLab Duo for a workflow powerhouse](https://about.gitlab.com/blog/2023/07/27/gitlab-flow-duo/) 에서 확인이 가능합니다.

<br>

---

<br>
<br>

이제는 저희 마이리틀돌 서버팀에서 사용하고 있는 Git Branch 전략에 대해 알아보겠습니다.

<br>

## 마이리틀돌 서버팀 Git Branch 전략

저희 개발팀은 Git Flow, Github Flow, GitLab Flow 모두 사용하고 있지 않습니다.

다만 각각의 특성을 조금씩 포함하고 있다고 말씀드릴 수 있을 것 같습니다.

<br>

### Rule

먼저 저희는 다음과 같은 rule을 정하여 Branch를 운용하고 있습니다.

> Feature Branch 명은 `feature/YYYYMMDD_주요기능` 혹은 `fix/YYYYMMDD_주요기능`의 형식을 따른다.
>
> Feature Branch는 local에서만 사용하고 원격에는 남겨두지 않는다.
>
> alpha Branch에 merge 할 때는 commit history를 남기며 merge하고 master Branch에 merge 할 때는 commit들을 squash 한 뒤에 merge를 진행하여 commit history를 깔끔하게 한다.
>
> Branch를 merge 하기 전에 항상 code review를 진행한다.

<br>

### Branch

- master Branch : 출시 가능 코드를 모아두는 항상 production-ready 상태인 Branch

- alpha Branch : development Branch와 비슷하지만 출시에 필요한 기능들을 test 하기 위한 서버 Branch

- Feature Branch : master Branch에서 분기한 Branch로 기능 개발, 오류 수정 등의 작업 진행

<br>

### Strategy

1. origin master Branch에서 local Feature Branch를 분기합니다.

2. local Feature Branch에서 기능 개발, 수정 등의 작업을 진행합니다.

3. 여러 명의 팀이 동시에 작업을 하므로 그대로 local Feature Branch를 origin alpha Branch에 merge 하려고 하면 conflict가 날 수 있습니다.

4. 이러한 경우를 생각하여 Feature Branch를 merge 할 때는 local alpha Branch로 checkout 한 뒤 origin alpha를 pull 하여 up-to-date 상태로 만든 뒤 merge 하려는 Feature Branch를 pull 하여 merge 합니다.

5. 이 때 발생하는 conflict가 있으면 해결한 뒤 origin alpha로 commit, push 를 진행합니다.

6. 이후 alpha Branch에 이어진 서버에서 QA를 진행합니다.

7. QA 도중 발견되는 버그는 마찬가지로 해당 Feature Branch 에서 작업한 뒤 다시 위의 작업들을 반복합니다.

8. QA가 완료된 기능에 한하여 해당 Feature Branch를 master Branch에 merge 합니다.

이러한 Git Branch 전략이 나온 이유는 먼저 마이리틀돌 팀의 개발, QA 여건이 고려되었습니다.

그리고 alpha, master Branch가 parallel 하게 진행이 되고 있고 alpha에 기능 개발, 오류 수정이 되는 대로 운영팀 허가만 나면 alpha Branch에 merge 할 수 있고 alpha Branch에서 QA가 완료되면 해당 Branch만 먼저 master Branch에 merge 할 수 있다는 장점을 가지고 있습니다.

또한 구조 또한 단순하여 쉽게 관리가 가능하다는 이유도 있습니다.

<br>

### Merge Conflict

앞서 Strategy 3번에서 언급했듯이 alpha Branch에 Feature Branch를 merge 할 때는 conflict 발생이 가능합니다.

<br>

<p align="center"><img src="https://s3.ap-northeast-1.amazonaws.com/tane.coconeimg.jp/img/git-conflict.png" width="500px"></p>

<br>

master Branch에서 분기한 Feature Branch X, Y를 개발자 둘이 각각 개발 중입니다.

Feature Branch X 작업이 먼저 완료되어 alpha에 Feature Branch X를 merge 하였습니다.

그 뒤 alpha에 Feature Branch Y를 merge 하려고 하는 경우 conflict가 발생합니다.

이 경우 alpha에 merge 할 때 PR(pull request)로 처리를 하려고 하면 문제가 복잡해집니다.

그러므로 Cli를 이용하여 local alpha Branch에서 해당 Feature Branch Y를 pull 받아와 conflict를 해결하고 origin alpha에 push 해주는 방식을 사용합니다.

이 때 local alpha Branch는 up-to-date 상태여야 합니다.

<br>

### 마무리

현재는 위와 같은 방식으로 Git Branch 전략을 가져가고 있지만 아직 부족하고 더 개선할 사항도 많다고 생각됩니다.

앞으로도 지속적으로 저희의 Branch 전략을 improve 할 것입니다.

Git Branch 전략은 각각의 상황에 맞게 customize가 꼭 필요하다고 생각합니다.

이 글을 읽어주신 분들도 저희의 Git Branch 전략을 참고해주시고 각각의 상황에 적절한 Git Branch 전략을 가져가시기를 바랍니다.

감사합니다!
