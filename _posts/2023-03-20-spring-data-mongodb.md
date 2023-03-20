---
layout: post
title : "Spring Data MongoDB 사용하기!"
comments: true
excerpt: "Spring Data MongoDB를 조사하면서 느낀 장단점과 유의 사항을 공유합니다."
date:   2023-03-20
tags: [MongoDB]
thumbnail: https://user-images.githubusercontent.com/93178834/226283280-18323cd8-42c5-4a9d-8440-1d4301394b2a.png
---

<p align="center"><img width="600" src="https://ifh.cc/g/TvHNHB.jpg"> </p>


안녕하세요😊 coconeM 신규개발팀의 서버 개발팀 입니다.

coconeM은 여러 서비스에서 메인디비로 MongoDB를 사용하고 있고, 제어는 MongoTemplate을 이용하고 있습니다.
그리고 MongoTemplate을 사용하면서 아래와 같은 불편한점과 문제점들이 있었습니다. <br>

1. 쿼리 작성하는데 적지 않은 시간을 사용하고 있음
2. 쿼리의 오류를 컴파일 타임에 알지 못함
3. 객체 중심 개발이 힘듬

그래서 저희팀에서는 Spring Data MongoDB를 도입하기 위해 R&D를 진행하였고, 그 과정에서 얻은 내용을 공유 드리겠습니다.

## Spring Data MongoDB란

---
Spring Data MongoDB는 스프링 프레임워크와 MongoDB 데이터베이스를 통합하는 기술로 `Repository 스타일`의 데이터 엑세스 레이어를 빠르고 간단하게<br>
구현할 수 있도록 도와주며, 코드의 가독성와 유지보수성을 높히고 MongoDB를 보다 효율적으로 활용할 수 있도록 도와주는 기능을 제공합니다.<br><br>

### Config

---
저희는 Spring Boot Version `2.7.7`, `Java 17` 를 사용하였습니다.
```
plugins {
        id 'java'
        id 'org.springframework.boot' version '2.7.7'
        id 'io.spring.dependency-management' version '1.0.15.RELEASE'
}

sourceCompatibility = '17'
        
dependencies{
        ...
        
        implementation'org.springframework.boot:spring-boot-starter-data-mongodb'
        
        ...
}
```
<br>

### Domain Class 설정

---
Repository를 사용하기 위해서 MongoDB의 컬렉션과 Mapping되는 `Domain Class`와 추상화 중심 인터페이스인 `Repository`를 설정해야 합니다.<br>
먼저 Domain Class 설정 방법에 대해 알아보겠습니다.<br>
Domain Class에 `@Document`을 사용하여 지정해줍니다. 별도의 Collection Name을 설정하지 않으면 기본으로 Class Name(camelcase)을 컬렉션명으로 사용합니다.<br>
`@Id`로 Collection의 기본키를 지정해주고, 필요한 경우 `@Field`로 필드명을 설정합니다. <br>

```java

@Document(collection = "User")
public class User {

    @Id
    private String id;
    @Indexed(unique = true)
    private Long userId;
    private String firstName;
    private String lastName;
    private int age;
    @Field("loc_list")
    private String[] locList;
    @Field("ct")
    @CreatedDate
    private LocalDateTime createTime;

    @Field("detail_id")
    @DocumentReference(lazy = true, lookup = "{ 'detailId' : ?#{#target} }")
    private UserDetail userDetail;

    @Version
    Long version;
}

@Document(collection = "UserDetail")
public class UserDetail {

    @Id
    private String id;
    @Field("detail_id")
    private Long detailId;
    private String loc;
    private String hp;
    @Field("ct")
    private LocalDateTime createTime;

    @Version
    Long version;
}
```

## Repository 설정

---
다음으로 Repository 설정 방법에 대해 알아보겠습니다.<br>

1. Repository는 `MongoRepository`, `PagingAndSortingRepository`, `CrudRepository`를 목적에 맞게 선택하고, Generic으로 `Domain Class` Type과 Domain Class의 `_id`
   Field Type을 설정합니다.
   + `PagingAndSortingRepository`의 paging 기능을 이용하면, skip 명령어가 실행되어 offset이 증가함에 따라 성능저하 현상이 발생할 수 있기 때문에 주의해서 사용 해야합니다.

2. Default로 기본적인 단건과 다건의 CRUD Method는 정의가 되어있고, Method 생성 규칙에 맞게 선언해서 사용하면 됩니다. 또한 사용자 지정 Method도 가능합니다.
   + [Defining Query Methods](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#repositories.query-methods.details)
3. [Supported Query Return Types](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#appendix.query.return.types)
4. @Query 사용
   + 특정 필드만 반환해야 한다면 `fields` option을 고려해야 합니다.(=Projection)
   + 배열 Field의 조회는 `$elemMatch`를 사용해야 합니다.

5. @Update를 이용하여 update문을 수행할 수 있습니다.
   + Repository에서 save를 지원함
      + 컬렉션에서 `_id` 기준으로 일치하는 Document가 존재하면 update가 되고, 없으면 Insert가 수행됩니다.
      + 지원되는 method : save(), saveAll()
   + 사용자 지정 method에는 Update가 동작하지 않기 때문에 `@Query`와 함께 사용해야 합니다.
      + 아래 두 Method는 같은 동작이 수행됩니다.
        ```java
        @Query("{'userId': ?0}")
        @Update("{ '$set' : { 'age' : ?1 } }")
        long updateByUserIdSetAge(Long userId, int age); // 사용자 지정 method
        
        @Update("{ '$set' : { 'age' : ?1 } }")
        long findByUserIdSetAge(Long userId, int age);
        ```
   + Collection의 Field가 많거나 특정 Field의 Size가 클 경우, @Update 사용을 통해 최적화가 가능합니다.


```java
public interface UserRepository extends MongoRepository<User, Long> {

    User findByUserId(Long userId);

    @Query(fields = "{'userId' : 1, 'firstName' : 1}")
    CompletableFuture<List<User>> findByFirstNameOrderByFirstNameAsc(String firstName);

    @Query("{'loc_list': {'$elemMatch' : {'$eq' : ?0}}}")
    List<User> findByLocElemMatch(String loc);

    @Query("{'userId': ?0}")
    @Update("{ '$set' : { 'age' : ?1 } }")
    long updateByUserIdSetAge(Long userId, int age); // 사용자 지정 method

    void deleteByUserId(Long userId);
}
```

<br>
이제 Service Layer에서 MongoDB의 Document 데이터 조회나 수정을 위해 만들어 둔 Repository 인터페이스를 사용할 수 있게됩니다.
<br>

```java
public class UserService {

    public UserResponse findUserByUserId(Long userId) {
        return UserResponse.builder().user(userRepository.findByUserId(userId)).build();
    }
}
```
<br>
다음으로 Spring Data MongoDB에서 데이터 모델링의 관계를 처리하기 위한 방법인 DocumentReference에 대해 알아보겠습니다.


## DocumentReference란?

---

1. @DocumentReference는 Document나 필드 사이의 `참조를 허용`하여 유연한 스키마를 사용할 수 있게 합니다. (DBRef를 사용할 때와 목표는 같지만 표현방식이 다릅니다.)
2. One-to-One, Many-to-One 및 Many-to-Many와 같은 일반적인 유형이 지원됩니다. <br>
   + 양방향 연관관계의 경우 `@ReadOnlyProperty`를 사용하여 해당 필드를 데이터베이스에 저장하지 않도록 막아 이중 참조를 방지 해야합니다.
      + 양쪽 필드가 서로 참조하게 되면 무한 루프에 빠질 수 있습니다.
   + `cascade`는 지원되지 않기 때문에 필요하면, AbstractMongoEventListener를 구현해서 사용하면 됩니다.
3. @DocumentReference는 3.3 version 부터 사용할 수 있습니다.

## DocumentReference 사용 방법과 유의 사항

---

1. Lazy Option
   + @DocumentReference에서 `lazy = true` 설정을 권장합니다.<br>
      + Lazy Option을 사용하면 모든 연관 엔티티를 한 번에 로딩하지 않고, 실제로 사용되는 시점에만 조회 쿼리를 실행하여 성능상의 이점이 존재합니다.

```
@Field("detail_id")
@DocumentReference(lazy = true, lookup = "{ 'detailId' : ?#{#target} }")
private UserDetail userDetail;
```

2. Data 형태

   + User Collection의 `detail_id` Field는 느슨한 연관관계의 형태로 `userDetail.detailId` 참조값을 갖고 있습니다.

```
{ 
    "_id" : ObjectId("640ad00528082669a6cb37e1"), 
    "userId" : NumberLong(1), 
    "firstName" : "Jeong", 
    "age" : NumberInt(18), 
    "loc_list" : [
        "seoul", 
        "busan"
    ], 
    "ct" : ISODate("2023-03-10T06:36:53.765+0000"), 
    "detail_id" : NumberLong(1), // userDetail.detailId
    "version" : NumberLong(0)
}
```

3. Lazy Test
   + Find Query Log를 확인해보면 Test Code의 `Thread.sleep(1000)` 때문에 1초 후에 연관관계 Find Query가 실행됩니다.
      + MongoDB의 Find Query를 확인하려면 `db.setLogLevel(1)`으로 Log Level 설정이 필요합니다.

```
    @Test
    public void user_reference_lazy_test() throws InterruptedException {
        //given
        long userId = 1L;
        //when
        User user = userRepository.findByUserId(userId);
        Thread.sleep(1000);

        //done
        log.info(user.getUserDetail()); // lazy option으로 참조 시점에 find query 실행
    }
    
   - MongoDB Find Query Log
   2023-03-10T15:36:53.835+0900 user command: find { find: "user", filter: { userId: 1 } ...
   2023-03-10T15:36:54.865+0900 userDetail command: find { find: "userDetail", filter: { userId: 1 } ...
```

4. N+1 문제

+ 연관관계를 설정한 Domain Class를 다건으로 조회 하고, 설정된 필드를 사용하면 N+1 문제가 발생합니다.
  ```
  List<User> findAllUser = userRepository.findAll();
  findAllUser.forEach(user -> log.info(user.getUserDetail())); // findAllUser.size() 만큼 UserDetail 조회 쿼리가 발생
  ```
+ 하지만 Spring Data MongoDB 에서는 Jpql의 Join Fetch와 같은 해결방법이 없습니다.
+ 명시적으로 Field에 데이터를 주입해주는 방법으로 해결할 수 있습니다.

```
    @Test
    public void list_user_reference_field_injection_test() {
        //when
        List<User> findAllUser = userRepository.findAll();
        
        // get userDetailList by userIDList
        List<Long> userIdList = findAllUser.stream().map(User::getUserId).toList();
        List<UserDetail> findUserDetailList = userDetailRepository.findByUserIdIn(userIdList);
        Map<Long, UserDetail> userDetailMap = findUserDetailList.stream().collect(Collectors.toMap(UserDetail::getUserId, Function.identity()));
        
        findAllUser.forEach(user -> user.setUserDetail(userDetailMap.get(user.getUserId()))); // field에 data 설정

        //done
        findAllUser.forEach(user -> log.info(user.getUserDetail()));
        
    }

- MongoDB Find Query Log
  COMMAND  [conn73] command user command: find { find: "user", filter: {}, ...
  COMMAND  [conn73] command userDetail command: find { find: "userDetail", filter: { userId: {  $in: [ 1, 2, 4, 3 ] } } ...

```

<br>
이제 다음으로 데이터베이스에서 동시에 여러 사용자가 같은 데이터를 수정할 때, 충돌이 발생하지 않도록 하는 방법 중의 하나인 `Optimistic Locking`에 대해 알아보겠습니다.
<br>

## Optimistic Locking 이란?

---
+ Optimistic Locking이란 여러 사용자가 동시에 같은 데이터를 수정할 때, 충돌이 발생하지 않도록 먼저 업데이트를 시도한 트랜잭션이 다른 트랜잭션 보다 우선권을 가지도록 하는 방법 입니다.
   1. 먼저 데이터를 읽은 후, 업데이트하기 전에 해당 데이터가 다른 사용자에 의해 변경되지 않았는지 확인합니다.
   2. 이를 위해 데이터베이스는 버전 정보를 사용하며, 엔티티를 저장할 때마다 버전 정보를 자동으로 증가시킵니다.
   3. 이를 통해 동시에 같은 데이터를 수정하려는 경우, 버전 정보가 다르기 때문에 충돌이 발생하게 됩니다.
   4. 이 때, Optimistic Locking 예외가 발생하게 됩니다.
      + WriteConcern level을 ACKNOWLEDGED로 설정해야 Exception을 감지할 수 있습니다.

<br>
위에서 말한 데이터베이스의 버전 정보를 통해 Optimistic Locking을 구현 하는 방법에 대해 알아보겠습니다.
<br>

```
@Version
Long version;
```

+ 사용법은 간단합니다. Domain Class에 위와같이 Verion 정보를 추가합니다. 
+ Optimistic Locking은 같은 Document에 여러 사용자가 동시에 read할 수 있고, update 하는 동안에만 Document를 잠금을 거는 기법입니다.
   + 실제 동작은 잠금을 건다기 보다 충돌 방지에 가까움 (잠금은 Pessimistic Locking)

### 특징
+ Transaction을 사용하는것보다 더 적은 비용이 발생합니다. 
+ 작업하고 있는 Document의 버전을 추적하고 버전이 변경되지 않은 경우에만 Document를 업데이트할 수 있는지 확인함으로써 데이터의 일관성이 보장됩니다.


### 유의 사항
Optimistic Locking은 데이터 충돌을 최소화하여 데이터의 일관성을 유지할 수 있고 Pessimistic Locking과 달리, 데이터를 읽을 때마다 잠금을 걸지 않기 때문에 동시에 같은 데이터를 읽는 경우 성능상의 이점이 있습니다.<br>
하지만, Optimistic Locking은 동시에 여러 사용자가 자주 같은 데이터를 수정하는 경우, 충돌이 발생할 가능성이 높아지고 충돌이 발생하면 다시 시도해야 하므로, 일부 성능 저하가 발생할 수 있어 주의하여 사용해야 합니다.



## example

1. Repository save
   + @Version을 추가한것 만으로도 save method가 호출되면 Optimistic Locking이 적용됩니다.
   + Save Command에서 Query의 조건으로 version이 추가되고, version 값은 증가됩니다.

```
    @Test
    public void optimistic_locking_jpa_test() {
        //given
        long userId = 4L;

        //when
        User updatableUser = userRepository.findByUserId(userId);
        User findUser = userRepository.findByUserId(userId);
        updatableUser.setAge(18);
        userRepository.save(updatableUser);

        //done
        Assertions.assertThrowsExactly(OptimisticLockingFailureException.class, () -> userRepository.save(findUser)).printStackTrace();
    }

- MongoDB Find Query Log
WRITE update User command: { q: { _id: ObjectId('64102dd4be1c8b2924d35920'), version: 0 }, u: { userId: 4, firstName: "Joe", age: 123456, loc_list: [ "seoul2", "busan" ], ct: new Date(1678781908193), detail_id: 4, version: 1 }, multi: false, upsert: false } ...
WRITE update User command: { q: { _id: ObjectId('64102dd4be1c8b2924d35920'), version: 0 }, u: { userId: 4, firstName: "Joe", age: 1, loc_list: [ "seoul2", "busan" ], ct: new Date(1678781908193), detail_id: 4, version: 1 }, multi: false, upsert: false } ...

- Exception Log
org.springframework.dao.OptimisticLockingFailureException: Cannot save entity 64102dd4be1c8b2924d35920 with version 1 to collection User. Has it been modified meanwhile?
```

2. Template update
   + Template의 Update, Upsert 명령어를 사용하면 Find Query에서 Version 정보를 감지 하지 않습니다.
     + Update나 Upsert를 수행할 경우 쿼리에 버전 정보를 추가해주어야 합니다.
   + Template도 Repository save method와 동일하게 동작합니다.

```
    @Test
    public void optimistic_locking_template_update_test() {
        //given
        long userId = 4L;

        //when
        User updatableUser = userTemplateRepository.findByUserId(userId);
        User findUser = userTemplateRepository.findByUserId(userId);
        updatableUser.setAge(18);
        userTemplateRepository.updateAgeByUserId(userId, updatableUser.getAge());

        //done
        Assertions.assertEquals(userTemplateRepository.updateAgeByUserId(userId, findUser.getAge()), 1);
    }
- MongoDB Find Query Log
WRITE update User command: { q: { userId: 4 }, u: { $set: { age: 18 }, $inc: { version: 1 } }, multi: false, upsert: true } ...
WRITE update User command: { q: { userId: 4 }, u: { $set: { age: 1 }, $inc: { version: 1 } }, multi: false, upsert: true } ...
```
<br>

## 그래서 Repository 방식이 더 좋나요?

---
Template은 복잡한 쿼리를 쉽게 작성할 수 있고, 세밀한 제어가 가능하다는 이점이 있고, Repository는 편리하고, 객체지향 개발을 가능하게 하는 이점이 존재합니다.<br>
따라서 어떤 방법이 더 맞다기 보다는 각 프로젝트의 특성에 맞게 채택하여 사용하시는게 좋을 것 같습니다.<br>
감사합니다.😇
<br><br><br>

#### References

---
[1] https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/ <br>
[2] https://www.concretepage.com/spring-5/spring-data-mongodb-query <br>
[3] https://medium.com/@andris.briedis/optimistic-locking-in-mongo-69fa693864ec <br>
