---
layout: post
title:  "Xcode 디버깅 팁"
comments: true
excerpt: "xcode 디버깅 팁이 궁금하다면!"
date:   2023-07-26 
tags: [xcode, debug, debugging] 
thumbnail: https://is1-ssl.mzstatic.com/image/thumb/Purple116/v4/58/25/3a/58253a1d-1cf2-c4a5-44a6-6bbc8ee6e274/Xcode-85-220-0-4-2x-sRGB.png/1200x630bb.png
---

안녕하세요.
CoconeM 헬로키티 스윗파티 클라이언트팀 입니다.<br/>
헬로키티 스윗파티는 Cocos2d-x엔진으로 개발이 되어서 주 개발 IDE로 xcode를 사용하고 있습니다.<br/>
xcode를 사용해서 개발하면서 특히, 디버깅할 때 팁을 몇 가지 공유해 보려고 합니다.<br/>
생각보다 모르고 계시는 분들이 꽤 있으시더라고요 ㅎㅎㅎㅎ

<br/><br/>

# Xcode Debugging Tip!

## 목차
- [Xcode Debugging Tip!](#xcode-debugging-tip)
  - [목차](#목차)
  - [디버깅 단축키 세팅하기](#디버깅-단축키-세팅하기)
    - [step over, step into step over(초기 세팅 O)](#step-over-step-into-step-over초기-세팅-o)
    - [continue(초기 세팅 X)](#continue초기-세팅-x)
    - [함수명 검색 (초기 세팅 X)](#함수명-검색-초기-세팅-x)
  - [디버깅 중 메모리 값 수정하기](#디버깅-중-메모리-값-수정하기)
  - [브레이크 포인트에 조건 추가하기](#브레이크-포인트에-조건-추가하기)
  - [특정 메모리 값이 변경 될때 브레이크 걸기](#특정-메모리-값이-변경-될때-브레이크-걸기)
  - [무한루프 쉽게 찾기](#무한루프-쉽게-찾기)
  - [마치며...](#마치며)
    
---
<br/><br/>

## 디버깅 단축키 세팅하기
디버깅하다 보면 손이 매우 바쁩니다.<br/> 
디버깅하는 라인도 넘어가야 하고 메모리값도 확인해야 합니다.<br/> 
이런 많은 일들을 마우스로만 하고 계신가요?<br/> 
왔다 갔다 손목도 아프고 피곤하고 **무엇보다 멋이 없습니다!!**<br/>
최소한 라인 넘기는 단축키는 기억해서 키보드로 해봅시다.<br/>
### step over, step into step over(초기 세팅 O)
  - ![step](https://github.com/YGyu/test2/assets/5143476/d98fd370-49fc-4d7d-96c6-144ffd9cada0)
  - ![step_over](https://github.com/YGyu/test2/assets/5143476/53817778-4f5c-4d31-ad8c-0ad3bd5c6f88)step over : 다음 라인으로 이동.
  - ![step_into](https://github.com/YGyu/test2/assets/5143476/f13da296-a16e-4a5a-af90-1a72e458afd5)step into : 현재 라인이 함수라면 함수 내부로 이동.
  - ![step_out](https://github.com/YGyu/test2/assets/5143476/1698dd9e-5cb1-4e70-ae88-2b4e6d4167d7)step out : 현재 함수를 빠져 나온다.
### continue(초기 세팅 X)
  - ![continue](https://github.com/YGyu/test2/assets/5143476/5b6cb514-04e6-430a-a58e-743b9e1d460b)
  - ![continue_img](https://github.com/YGyu/test2/assets/5143476/9ca06c49-38bf-4d85-94b9-e57c1de2afed)continue : 브레이크를 풀고 실행.
### 함수명 검색 (초기 세팅 X)
  - ![search_key](https://github.com/YGyu/test2/assets/5143476/79549f69-35ab-4979-9717-1bfb1d45f5f8)
  - ![search_sample](https://github.com/YGyu/test2/assets/5143476/8b243a48-b39a-4856-86e4-8a13e77a2483)
  - 이것은 디버깅은 아니지만 단축키로 설정하면 매우 유용한 기능이라 추가로 알려드립니다.
  - 현재 파일의 정의된 함수를 검색할 수 있는 기능으로 단축키 누르고 생각나는 함수 이름을 타이핑하면 함수가 검색되고 Enter를 누르면 해당 함수로 이동됩니다.

<br/><br/>

## 디버깅 중 메모리 값 수정하기
- ![change](https://github.com/YGyu/test2/assets/5143476/ad746b66-ca46-4c33-9cb3-c07c5757f6dc)
- 매우 쉽습니다.
- 아래 debug area에서 원하는 변수에서 더블클릭 or 마우스 우클릭으로 Edit Value로 변경하면 됩니다.
- int, float같이 숫자 형식의 값만 변경할 수 있습니다.
- 하지만 UI 작업 중에 위치 계산이나, 캐릭터 좌표 계산 같은 내용을 디버깅 중이라면 매우 매우 유용하게 사용할 수 있는 기능입니다.
<br/><br/>

## 브레이크 포인트에 조건 추가하기
- ![condition](https://github.com/YGyu/test2/assets/5143476/a3ba2014-cd75-4ca8-8192-ee4499745971)
- 이것도 쉽습니다.
- 브레이크 포인터에 마우스 우클릭으로 나오는 메뉴에서 Edit Breakpoint...를 누르면 나오는 메뉴에서 condition에 조건을 넣으면 됩니다.
- 특정 조건이 될 때를 찾기 좋은 기능이긴 하지만 자주 호출이 되는 곳이라면 매우 매우 느려지니 주의가 필요합니다.
<br/><br/>
 
## 특정 메모리 값이 변경 될때 브레이크 걸기
- ![watch](https://github.com/YGyu/test2/assets/5143476/ad807a9b-bbae-4eb7-9956-74f36204778e)
- 어떠한 변수의 값(메모리의 값)이 변경되는 시점을 찾고 싶을 때 매우 유용한 기능입니다.
  - 메모리가 지워지는 시점이라던지...
  - 어떠한 값이 변경되면 안 되는 시점인데 변경이 되어서 버그가 생기는데.. 그 타이밍을 도저히 못 찻는 상황이라던지...
- 사용한 이후에 브레이크 포인터 리스트에서 꼭 삭제해 주시는 것이 심신건강에 좋습니다.
<br/><br/>

## 무한루프 쉽게 찾기
- 나도 모르게 for문 무한 루프 버그를 만들거나 어디선가 while이 무한루프 타는 것 같은데 어딘지 모르겠다 싶을 때 사용하면 됩니다.
- 이럴 때는 무조건 cpu가 100% 치는데요, Profile in Instruments를 사용하는 것입니다.
- ![loop](https://github.com/YGyu/test2/assets/5143476/4cdb99a0-2f87-4360-8b47-3a7a08dd5d34)
<br/><br/>

## 마치며...
자주 사용하는 기능이지만 글로 설명하려니 쉽지 않군요.<br/>
위에 적은 기능들을 모르셨던 분들은 꼭! 써보시기를 추천해 드립니다.<br/>
특히 [함수명 찾기](#함수명-검색)과 [메모리 변경 시 자동 브레이크](#특정-메모리-값이-변경-될때-브레이크-걸기) 기능은 사용 전과 후의 차이가 하늘과 땅이라고 생가하기 때문에 꼭! 꼭! 한번 사용해 보세요. 