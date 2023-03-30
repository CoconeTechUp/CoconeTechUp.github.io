---
layout: post
title: "Shader Graph를 활용한 이미지 색상 변경"
comments: true
excerpt: "이미지 색상변경에는 3가지 방법이 있습니다."
date: 2023-03-30
tags: [Unity, Shader Graph, Util]
thumbnail: https://images.contentstack.io/v3/assets/blt08c1239a7bff8ff5/bltdff1a2920dd347a5/63f5068a97790d11728d0a6d/U_Logo_Small_black.svg
---

안녕하세요.<br/>
코코네엠의 유니티 개발팀입니다.<br/>
이번 테크 블로그에서는 유니티 쉐이더 그래프를 활용한 이미지 색상 변경에 대해 알아보겠습니다.<br/>
<br/>

# 1. 들어가기 전에

이미지 색상 변경하는 방법은 add, multiply, hsv 3가지 정도로 나눌 수 있을 것 같습니다. <br/>
먼저 기본적인 쉐이더 그래프의 모습을 살펴보겠습니다.
![img](https://user-images.githubusercontent.com/93178834/228719207-0c050b84-b26d-4e61-b9ea-2ea09e1b278f.png)

Input으로 MainTexture와 색상 변경을 위한 파라미터를 받아서, 색상 변경을 위한 SubGraph(위 예제에서는 Hsv Node)를 통과한 값을 Fragment의 Base Color에 지정합니다. 여기서 Alpha값은 변경되어서는 안되기 때문에 MainTexutre의 Alpha값을 그대로 Output으로 내보냅니다.

# 2. Color Add 방식

Color Add 방식의 SubGraph는 아래 예시와 같습니다.
![img](https://user-images.githubusercontent.com/93178834/228719432-19ad72e1-be6e-47ed-aa52-ffa5b66db2dd.png)
기본적인 원리는 원래의 색상에 Input으로 들어온 컬러값을 그대로 더해주는 것입니다.<br/>
예를 들어 검은색(RGB : 0, 0, 0) + 흰색(RGB : 1, 1, 1) 하면 흰색이 되는 원리인 것입니다. <br/>
주의할 것은, 원본이미지가 가능한 검은색에 가까워야 원하는 색으로 변경이 가능하다는 것입니다. <br/>
이 방식은 원본색상보다 밝은 색상으로만 변경이 가능합니다.
만약에 원본 이미지가 흰색이라면 이 방식으로 어떠한 색상으로도 변경이 어렵습니다. 흰색에는 어떠한 색을 더해도 그대로 흰색이기 때문입니다. <br/>
원본이미지가 흰색 계열인 경우 사용할 수 있는 방법으로 Multiply 방식이 있습니다. 아래에서 설명하겠습니다.

# 3. Color Multiply 방식

Color Multiplay 방식에서 사용되는 SubGraph는 다음과 같습니다.
![img](https://user-images.githubusercontent.com/93178834/228719613-533639c0-379b-4d6e-9ce1-44451f2c1b7f.png)
이 방식은 1에 어떤한 값을 곱하면 그 값이 그대로 나오는 원리(1 _ a = a)를 이용한 것입니다. 컬러의 각 요소의 범위는 0~1이기 때문에 이 방식은 색이 점점 어둡게 변해갑니다. 그러므로 원본 이미지를 가능하면 1에 가까운 색 - 흰색 계열 - 로 제작을 해야 원하는 결과값을 얻을 수 있습니다. 만약에 원본이 검은색이라면 결과값도 검은색일 것입니다. (0 _ a = 0)

# 4. HSV Control 방식

제작된 이미지가 흰색이나 검은색 계열이 아닌 경우에는 HSV(Hue, Saturation, Value)를 조절하여 원하는 결과값을 얻을 수 있습니다.<br>
이 경우의 SubGraph는 다음과 같습니다. (조금 복잡해보이지만 기본적으로 위의 SubGraph와 동일한 원리입니다.)
![img](https://user-images.githubusercontent.com/93178834/228719550-dd5189a0-6b11-43c6-bb18-d0ccf2fc0f25.png)

HSV를 사용하기 위해서는 먼저 RGB를 HSV로 변경해야 합니다. 유니티에서는 Colorspace Conversion Node에서 이것이 가능합니다. 그 다음에 Input으로 들어온 Hue(0~1), Saturation(-1~1), Value(-1~1) 값을 Vector로 변환합니다. 그 다음에 두 벡터값을 더해서 RGB로 변경 후에 Output으로 내보냅니다. Saturation과 Value값의 범위가 -1~1인 것은 두 값의 경우 원하는 값으로 변경하기 위해서 빼야하는 경우도 있기 때문입니다. 즉 최대값 1에서 0으로 만들기 위한 -1에서 최소값 0을 최대값 1로 만들기위한 1이 최소값이 되는 것입니다.

# 5. 마무리

짧게 유니티에서 이미지 색상변경 방법에 대해서 간단히 알아봤습니다. 이 내용을 응용하면 이미지 내에서 일부분만 색상 변경을 바꾼다든지, 한가지 색상이 아니라 여러가지의 색상을 적용한다든지의 것들이 가능해집니다. 다음 블로그에는 이 부분과 관련된 내용으로 다시 찾아뵙겠습니다.<br/>
읽어주셔서 감사합니다.<br/>
