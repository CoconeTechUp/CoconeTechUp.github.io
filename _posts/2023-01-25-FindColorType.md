---
layout: post
title:  "색상표에서 유사 색상을 찾아서 분류하는 간단한 방법"
comments: true
date:   2023-01-25
tags: [Client, C#, Color, NFT]
thumbnail: https://user-images.githubusercontent.com/93178834/214565460-979b0f48-ea62-4168-91f9-3e0ad6097c46.png
---

안녕하세요. 신규 서비스 개발실의 클라이언트 개발자 입니다. 

저는 NFT 민팅 관련 기능을 개발 할 때 고민했었던 색상 분류 방법에 대한 내용을 공유해보려고 합니다.
<br/><br/>

# 배경
이번에 작업중인 프로젝트에서는 다양한 아이템을 NFT로 민팅하고 거래를 할 수 있는 전용 마켓(jellyme[[1]][jellyme])이 있습니다. 마켓에서 15종류의 색상 필터로 구분할 수 있도록 민팅하는 모든 리브리에 대해 색상 타입을 넣어야 했습니다.

<img width="230" alt="01_jellyme_filter" src="https://user-images.githubusercontent.com/98134855/214467199-07e77d42-3551-4173-89d7-ebaba351002c.png">

하지만 리브리는 먹는 먹이 종류에 따라서 색상이 변하기 때문에 다양한 색상으로 변할 수 있고, 그 말은 모두 다른 RGB값을 갖고 있다는 의미이기도 합니다. 그래서 리브리의 색상값으로 색상표에 맞는 색상 타입을 찾아내는 기능이 필요했습니다.

<img width="350" alt="02_livly_food" src="https://user-images.githubusercontent.com/98134855/214482645-9fded6a7-6893-4de4-937b-f6849b820f49.png">
<br/><br/>

# 리브리의 메인 색상 찾기
일반적인 RGB 색상값은 0~255 사이의 값을 갖지만, 리브리의 경우 리브리 종 별로 고유의 색상 범위를 갖고 있습니다. 

기본색상|최소범위|최대범위
---|---|---|
![livly_color_origin]|![livly_color_min]|![livly_color_max]

그렇기에 현재 리브리의 색상값을 그대로 사용할 수는 없고 정규화하는 작업이 필요했습니다. 그래서 리브리의 현재 RGB값을 최대값으로 나눠서 0~1의 범위의 값으로 변환하고 다시 255를 곱해서 리브리의 메인 색상값을 추출했습니다.

```C#
static Color GetNormalizeColor(Color mainColor, Color maxColor)
{
    Color normalizeColor = new()
    {
        r = mainColor[0] / maxColor[0],
        g = mainColor[1] / maxColor[1],
        b = mainColor[2] / maxColor[2]
    };

    return normalizeColor * 255f;
}
```
<br/><br/>

# 색상표에서 메인 색상과 유사한 색상 타입 찾기
색상표에서 알맞은 색상 값을 찾기 위해 모든 색상값에 Range를 두고 비교하는 작업은 너무 번거롭고 비효율적인 과정입니다. 

<img width="233" alt="06_color_table" src="https://user-images.githubusercontent.com/98134855/214482640-456e39b1-a47d-4ccb-acb4-59b58864f5fa.png">
<br/><br/>

```C#
public static readonly Dictionary<string, Color> ColorTag = new()
{
    {"Black", new Color(0, 0,0)},
    {"White", new Color(255, 255,255)},
    {"Gray", new Color(127, 127,127)},
    {"Red", new Color(255, 0, 0)},
    {"Orange", new Color(255, 127, 0)},
    {"Yellow", new Color(255, 255, 0)},
    {"Lime", new Color(127, 255, 0)},
    {"Green", new Color(0, 255, 0)},
    {"Turquoise", new Color(0, 255, 127)},
    {"Cyan", new Color(0,255,255)},
    {"Ocean", new Color(0,127,255)},
    {"Blue", new Color(0, 0,255)},
    {"Violet", new Color(127, 0,255)},
    {"Magenta", new Color(255, 0,255)},
    {"Raspberry", new Color(255, 0,127)},
};
```

그래서 고민 끝에 RGB값을 3차원 좌표로 변환하여 메인 색상값과 색상표에 값들과 두 점사이의 거리 값을 계산하고 가장 짧은 값을 찾아보기로 했습니다. 거리(distance)는 일종의 유사도(similarity) 개념이기 때문에 거리가 가까울수록 그 특성(feature)들이 비슷하다는 뜻이기 때문에 K-최근접 이웃(K-Nearest Neighbor)[[2]][k_nearest] 알고리즘 같이 머신러닝 알고리즘에서도 많이 사용됩니다.

```C#
public static string GetNearestColor(Color inputColor)
{
    string findColor = null;
    float minDist = float.MaxValue;
    float curDist = minDist;

    foreach (var colorData in LivlyDefine.ColorTag)
    {
        curDist = Mathf.Sqrt(Mathf.Pow(inputColor.r - colorData.Value.r, 2) +
                            Mathf.Pow(inputColor.g - colorData.Value.g, 2) +
                            Mathf.Pow(inputColor.b - colorData.Value.b, 2));

        if (minDist > curDist)
        {
            minDist = curDist;
            findColor = colorData.Key;
        }
    }

    return findColor;
}
```

확인해보니 Red, Blue, Black, Lime 등 표에 정의된 값에 가까울 수록 정확한 결과를 보여주었지만 애매하게 푸른빛이 도는 회색을 Gray가 아닌 다른 색으로 찾는 증상이 나타나기 시작했습니다. 여러가지 샘플을 테스트해보고 회색이 나와야할 색들에서 문제가 생긴다는 것을 확인했습니다.
<br/><br/>

# 문제 수정
(127, 127, 127) 뿐만 아니라 (40, 40, 40)이나 (200, 200, 200) 같이 명도의 차이만 있고 RGB의 각 값이 유사한 경우에도 모두 회색으로 처리해야 한다는 것을 확인했습니다. 그래서 무채색 종류를 먼저 처리해주는 작업을 추가하기로 했습니다. 무채색의 경우 세 값이 모두 비슷한 상태를 갖기 때문에 R, G, B의 표준편차[[3]][standard_deviation] 값으로 구분을 해봤습니다.

```C#
static float GetStandardDeviation(IEnumerable<float> sequence)
{
    if (!sequence.Any()) return 0f;

    var sum = sequence.Sum(d => Mathf.Pow(d - sequence.Average(), 2));
    return Mathf.Sqrt(sum / sequence.Count());
}
```

그리고 표준편차가 일정 수치보다 낮은 경우 무채색으로 분류하고, black, gray, white 값을 구분했더니 다양한 색상 상태를 모두 알맞게 분류하는 것을 확인할 수 있었습니다. 수정된 최종 코드는 다음과 같습니다.

```C#
public static string GetNearestColor(Color targetColor)
{
    string[] precheckColors = LivlyDefine.ColorTag.Where(x => x.Value.r == x.Value.g && x.Value.g == x.Value.b).Select(x => x.Key).ToArray();

    // Black, White, Gray 검사
    // r, g, b 값이 유사하다면 세 가지 색상에 해당한다
    List<float> targetColorRGB = new() {targetColor.r, targetColor.g, targetColor.b};
    float standardDeviation = GetStandardDeviation(targetColorRGB);
    if (standardDeviation < 15f)
    {
        const float endRange = 20f; // Black, White 값 판정 범위

        // Black
        if (targetColorRGB.Average() < endRange)
            return precheckColors[0];

        // White
        if (targetColorRGB.Average() > 255f - endRange)
            return precheckColors[1];

        // Gray
        return precheckColors[2];
    }

    // Table에 있는 Color Type 검사
    string findColor = null;
    float minDist = float.MaxValue;
    float curDist = minDist;

    foreach (var colorData in LivlyDefine.ColorTag)
    {
        if (precheckColors.Any(x => x.Equals(colorData.Key)))
            continue;

        curDist = Mathf.Sqrt(Mathf.Pow(targetColor.r - colorData.Value.r, 2) +
                            Mathf.Pow(targetColor.g - colorData.Value.g, 2) +
                            Mathf.Pow(targetColor.b - colorData.Value.b, 2));

        if (minDist > curDist)
        {
            minDist = curDist;
            findColor = colorData.Key;
        }
    }

    return findColor;
}
```
<br/><br/>

# 마치며
이번 포스팅에서는 지정된 색상표에서 입력된 RGB값으로 알맞는 색상 타입을 추출해내는 방법에 대해 살펴봤습니다. 복잡한 조건문을 설정하거나 어려운 머신러닝 같은 방법이 아니라도 간단하게 분류가 가능하는지에 대해서 공유할 수 있었습니다.

주의할 부분은 무채색에서 3가지 타입을 구분할 때 단순하게 양 끝에 가깝지 않다면 Gray로 처리했으나 Light Gray, Dark Gray 등 단계가 세분화되면 별도의 처리가 필요로 합니다.

이후에 든 생각은 무채색 구분을 할 때 표준편차가 아닌 HSV 변환으로 채도(Saturation)를 기준으로 구분하는 방법이나 다양한 다른 방법도 있었을 것 같습니다.
<br/><br/>

# Reference
[1] : [https://jellyme.io/][jellyme]<br/>
[2] : [https://ko.wikipedia.org/wiki/K-%EC%B5%9C%EA%B7%BC%EC%A0%91_%EC%9D%B4%EC%9B%83_%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98][k_nearest]<br/>
[3] : [https://ko.khanacademy.org/math/statistics-probability/summarizing-quantitative-data/variance-standard-deviation-sample/a/population-and-sample-standard-deviation-review][standard_deviation]<br/>

<!-- link -->
[jellyme]: https://jellyme.io/
[k_nearest]: https://ko.wikipedia.org/wiki/K-%EC%B5%9C%EA%B7%BC%EC%A0%91_%EC%9D%B4%EC%9B%83_%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98
[standard_deviation]: https://ko.khanacademy.org/math/statistics-probability/summarizing-quantitative-data/variance-standard-deviation-sample/a/population-and-sample-standard-deviation-review

<!-- image -->
[livly_color_origin]: https://user-images.githubusercontent.com/98134855/214482637-2c9fbf47-9061-4a81-abad-5083d0a7ea02.jpg "Livly color - Origin"
[livly_color_min]: https://user-images.githubusercontent.com/98134855/214482632-9a5a6ddd-c117-43b2-be8a-07e2aa1351f4.jpg "Livly color - Min"
[livly_color_max]: https://user-images.githubusercontent.com/98134855/214482625-40f09f61-13e1-4785-a8c2-01c582a728cb.jpg "Livly color - Max"
