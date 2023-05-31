---
layout: post
title:  "RedisTemplate에 대해 알아보자"
comments: true
excerpt: "RedisTemplate은 Spring Data Redis의 중심적인 클래스 중 하나로"
date:   2023-05-31 
tags: [Redis, RedisTemplat에] 
thumbnail: https://www.vinsguru.com/wp-content/uploads/2020/03/redis-stream.png
---

안녕하세요

CoconeM 헬로키티 스윗파티 서버팀입니다.

헬로키티 스윗파티에서는 MongoDB 와 Redis를 사용하는데요

오늘은 SpringBoot에서 Redis를 사용하기 위한 RedisTemplat에 대해서 알아보겠습니다.

<br/><br/>

# RedisTemplate에 대해 알아보자

## 목차
- [RedisTemplate에 대해 알아보자](#redistemplate에-대해-알아보자)
  - [목차](#목차)
  - [RedisTemplate이란?](#redistemplate이란)
  - [RedisTemplate의 장점](#redistemplate의-장점)
  - [RedisTemplate의 주요 기능](#redistemplate의-주요-기능)
    - [Value Operations](#value-operations)
    - [List Operations](#list-operations)
    - [Hash Operations](#hash-operations)
    - [ZSet Operations](#zset-operations)
  - [유의사항](#유의사항)

---
<br/>

## RedisTemplate이란?
<br/>
RedisTemplate은 Spring Data Redis의 중심적인 클래스 중 하나로, Redis에 대한 다양한 연산을 지원합니다. <br/>이는 연결 관리, 직렬화/역직렬화, 리소스 관리를 처리해주며, 다양한 데이터 구조 연산을 제공합니다.  
<br/><br/>

## RedisTemplate의 장점
<br/>
RedisTemplate의 주요 장점은 다음과 같습니다:<br/><br/>

- **추상화**: RedisTemplate은 Redis에 대한 저수준 상세 사항들을 추상화하여, 개발자가 비즈니스 로직에만 집중할 수 있도록 합니다.
- **리소스 관리**: Redis 연결과 같은 리소스를 알아서 관리해줍니다.
- **직렬화와 역직렬화**: RedisTemplate는 데이터를 Redis에 저장하거나 Redis에서 데이터를 가져올 때 필요한 직렬화와 역직렬화를 알아서 처리해줍니다.
- **예외 처리**: RedisTemplate은 예외 처리도 담당해주며, 스프링의 DataAccessException 계층구조로 예외를 변환합니다.

<br/><br/>
## RedisTemplate의 주요 기능
<br/>
RedisTemplate은 다양한 Redis 연산을 지원합니다. 
<br/>몇 가지 주요 기능들과 메서드 예제입니다
<br/><br/>
 
### Value Operations
<br/>
Value 연산은 가장 단순한 형태의 연산입니다. opsForValue() 메서드를 통해 ValueOperations 인스턴스를 가져올 수 있습니다.

```java
    RedisTemplate<String, String> redisTemplate;
    ValueOperations<String, String> ops = redisTemplate.opsForValue();
    ops.set("key", "value");
    String value = ops.get("key");
```
### List Operations
<br/>
List 연산은 Redis의 List 타입에 대한 연산을 제공합니다. opsForList() 메서드를 통해 ListOperations 인스턴스를 가져올 수 있습니다.

```java
RedisTemplate<String, String> redisTemplate;
ListOperations<String, String> ops = redisTemplate.opsForList();
ops.rightPush("key", "value1");
ops.rightPush("key", "value2");
String value1 = ops.leftPop("key");
```
### Hash Operations
<br/>
Hash 연산은 Redis의 Hash 타입에 대한 연산을 제공합니다. opsForHash() 메서드를 통해 HashOperations 인스턴스를 가져올 수 있습니다.

```java
RedisTemplate<String, Object> redisTemplate;
HashOperations<String, String, Object> ops = redisTemplate.opsForHash();
ops.put("key", "hashKey", "value");
Object value = ops.get("key", "hashKey");
```
### ZSet Operations
<br/>
ZSet 연산은 Redis의 ZSet 타입에 대한 연산을 제공합니다. <br/>
opsForZSet() 메서드를 통해 ZSetOperations 인스턴스를 가져올 수 있습니다. <br/>
ZSet은 각각의 멤버가 score 값과 연관되어 있어 score에 따라 멤버들을 정렬하는 것이 특징이므로, 랭킹 시스템이나 스코어보드 등에 활용할 수 있습니다.

```java
RedisTemplate<String, String> redisTemplate;
ZSetOperations<String, String> ops = redisTemplate.opsForZSet();
ops.add("key", "value1", 1.0);
ops.add("key", "value2", 2.0);
Set<String> values = ops.range("key", 0, -1); // 모든 요소를 오름차순으로 가져옵니다.
```
<br/><br/>

## 유의사항
<br/>

- **비동기 처리 미지원**: RedisTemplate는 동기적인 작업만 지원하며, <br/>비동기 처리를 위해서는 별도의 방법을 사용해야 합니다.
- **세션 관리**: RedisTemplate 사용 후에는 반드시 'close()' 메소드를 호출하여 Redis 연결을 종료해야 합니다. 그렇지 않으면 연결이 계속 열려 있어 리소스를 과도하게 사용하게 됩니다.
- **직렬화 메커니즘**: RedisTemplate는 기본적으로 JdkSerializationRedisSerializer를 사용하여 직렬화를 수행합니다. <br/>이는 Java 객체를 직렬화하는 가장 일반적인 방법이지만, 객체의 구조가 복잡하거나 크기가 큰 경우 성능 저하를 일으킬 수 있습니다. <br/>따라서 상황에 따라 적절한 직렬화 메커니즘이 필요합니다.
- **Thread Safety**: RedisTemplate는 thread-safe하지 않습니다. 따라서 멀티 스레드 환경에서는 주의해야 합니다.