---
layout: post
title: "Protobuf를 알아보자"
comments: true
excerpt: "Protobuf를 간단히 알아보자"
date: 2023-04-21
tags: [Protobuf, Protocol Buffers]
thumbnail: https://user-images.githubusercontent.com/60130985/233550235-2793e8ad-746b-47a4-bc7f-4e3c09969020.png
---

안녕하세요 🙋 </br>
CoconeM 헬로키티 스윗파티 서버팀입니다.

헬로키티 스윗파티에서는 Client와 통신을 위해 Google에서 개발한 Protobuf를 사용하고 있어요.

오늘은 Protobuf가 어떤 것인지, 어떻게 사용하는지 알아봐요 🧏‍♀️

## Protobuf 소개

Protobuf는 Google에서 개발하고 오픈소스로 공개한 `데이터 직렬화 구조`에요. </br>
대표적인 데이터 직렬화 방식은 우리가 잘 알고 있는 `JSON과 XML`이 있어요.

`직렬화`는 데이터 표현은 `Byte 단위`로 변환하는 작업을 의미해요.

### Protobuf의 좋은점

Protobuf는 이진 포맷이라서 JSON, XML보다 더 작은 데이터 용량을 차지하고, 속도도 빠르고 효율적이에요.</br>
구조화된 데이터는 직렬화, 역직렬화를 하는 데 효율적이며, 다양한 언어에서 Protobuf 라이브러리를 제공하므로, 다른 언어 간의 데이터 교환도 용이해요.</br>

다른 언어 간 데이터 교환이 필요하다면 Protobuf + gRPC로 별도의 변환 없이 통신이 가능해요.

### Protobuf의 나쁜점

JSON은 읽기 편한 상태로 되어 있어 가독성이 뛰어난 반면 Protobuf는 .proto 파일이 없으면 암호 해독 정도로 가독성이 떨어져요.

<img width="600" alt="image" src="https://user-images.githubusercontent.com/60130985/233552393-39dea6a9-e8ab-47a7-a079-b9e7fb5ae871.png">

새로운 문법도 익혀야 하고, JSON처럼 대중화되어 있지 않아요.

---

## Protobuf 문법

데이터 구조를 Message로 정의해야 해요. Message는 Field로 구성되어 있고, 이름과 값을 가져요.</br>
Field는 각각 고유한 번호를 부여받아야 해요.(ItemAmount의 1, 2번)
</br>

### 데이터 구조

> int32, int64, uint32, uint64 : 정수형 필드 타입. </br>
> float, double : 부동소수점 필드 타입. </br>
> bool : Boolean 필드 타입. </br>
> string : 문자열 필드 타입. </br>
> bytes : 바이트 배열 필드 타입. </br>
> enum : 열거형 필드 타입. </br>
> repeated : 배열 형태 필드 타입. </br>
> message : 다른 메시지를 포함하는 필드 타입.

</br>

### Message 정의

Cocone M에서는 Message는 CamelCase로, Field는 SnakeCase로 정의하고 있어요. </br>

```
syntax = "proto3"; // ver3 사용 명시
package test.proto;
import "Enum.proto"; // 다른 proto file import

// ItemAmount 정의
message ItemAmount {
    string item_id = 1;
    int32 amt = 2;
}

// ItemAount 사용
message BuyItemInfo {
    ItemAmount item = 1;
    int32 prc = 2;
}

// repeated 사용하여 ItemAmount 배열 형태 구조 정의
message ItemInfo {
    ItemAmount main_item = 1;
    repeated ItemAmount items = 2;
}

// enum 정의
enum Status {
  UNKNOWN = 0;
  ACTIVE = 1;
  INACTIVE = 2;
}
```

### Protobuf 변환

Client와 통신을 Protobuf로 하기 때문에 DataBase에서 데이터를 조회하여 Java Class를 Protobuf로 변환해서 반환해야 해요.</br>

ItemAmount를 변환한다면 아래 코드와 같아요.</br>
실제 비즈니스 코드 상에서는 Converter를 구현하여 Java Class <-> Protobuf 변환을 하고 있어요.

```
// Java Class
public Class ItemAmount {
    private String itemId;
    private int amt;
}

// Java to Protobuf
ExampleProto.ItemAmount.Builder builder = ExampleProto.ItemAmount.newBuilder();

builder.setItemId("Example Item");
builder.setAmt(1);

ExampleProto.ItemAmount example = builder.build();
```

### 글을 마치며

Protobuf에 대해 알아보는 데 도움이 되었으면 좋겠습니다.</br>
Protobuf와 gRPC를 사용해보시는 건 어떨까요?
감사합니다.
