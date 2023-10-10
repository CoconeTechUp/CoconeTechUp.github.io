---
layout: post
title: "MongoDB oplog 로 특정 컬렉션 복원하기"
comments: true
excerpt: ""
date: 2023-10-04
tags: [MongoDB]
thumbnail: https://github.com/cocone-m/techup.github.io/assets/147461874/2fdc6c89-f02c-40d1-b3a4-4f48309bf321
---

안녕하세요, SRE 팀의 DBA 입니다.
이 글에서는 COCONE-M 에서 사용하고 있는 메인 DB 인 MongoDB Oplog 를 통해 복구하는 방법에 대해 공유해보자가 합니다.

## Oplog 란?

---

operations log 의 약자로 Replica set 내부에서 발생하는 모든 동작에 대한 것들을 기록한 로그 입니다.
이 로그는 옵션에 따라 용량을 정해줄 수 있으며 저희는 기본적으로 하루~이틀정도의 양을 남겨놓도록 하고 있습니다.
용량이 꽉차게 되면 가장 오래된 순으로 자동으로 데이터가 삭제되게 됩니다.

Replica set 의 primary 와 secondary 에서만 확인이 가능하며,
local DB 내에 oplog.rs 라는 컬렉션에서 데이터 확인이 가능합니다.

```
syf_s3:PRIMARY> use local
switched to db local
syf_s3:PRIMARY> db.oplog.rs.findOne()
{
	"ts" : Timestamp(1696226021, 1389),
	"t" : NumberLong(9),
	"h" : NumberLong("-8838505616788674286"),
	"v" : 2,
	"op" : "d",
	"ns" : "DB명.컬렉션",
	"ui" : UUID("67c6fd93-6532-4674-9737-199d89c46ea8"),
	"wall" : ISODate("2023-10-02T05:53:41.222Z"),
	"o" : {
		"_id" : ObjectId("6511204a5813c9515a214114")
	}
}
```


## Oplog 로 복원하는 방법은?

---
Oplog 를 통해 복원하기 위해서는 몇가지 필요한 것이 있습니다.
Oplog 가 커버가능한 범위 내의 전체 백업 데이터 & 특정 시점까지의 Oplog 가 필요합니다.
위에서 설명하였듯 Oplog 는 모든 동작에 대한 것들을 기록하여 가지고 있는 것은 맞습니다만, 모든 기간을 가지고 있지는 않습니다.
하루 혹은 이틀정도의 Oplog 만 가지고 있기 떄문에 롤백해야하는 시점이 이를 넘어갈 경우 Oplog 를 통해서 백업은 불가능합니다.
그렇기 때문에 Oplog 가 커버가능한 시점의 전체 백업과 돌리고 싶은 시점까지의 Oplog 로 복원을 진행합니다.

예를 들어 DB 를 잘못 날렸다면?

1. mongodump 를 통해 oplog.rs 컬렉션을 덤프
```
mongodump --host <host> --username <user> --password <pwd> --authenticationDatabase admin -d local -c oplog.rs -o /home/mongodb/dump/
```

2. DB drop 한 명령어가 언제 실행되었는지 oplog 에서 확인
```
syf_s3:PRIMARY> db.oplog.rs.find({"o":{"dropDatabase":1}}).pretty()
{
	"ts" : Timestamp(1696477032, 79),
	"t" : NumberLong(1),
	"h" : NumberLong("-3245798297464080843"),
	"v" : 2,
	"op" : "c",
	"ns" : "ME_SPARTY_CMS.$cmd",
	"wall" : ISODate("2023-10-05T03:37:12.637Z"),
	"o" : {
		"dropDatabase" : 1
	}
}
```
위의 내용 중 ts 의 1696477032 값을 확인

3. 해당 값 전까지의 oplog 를 다시 복원
```
mongorestore --host <host> --username <user> --password <pwd> --authenticationDatabase admin --oplogReplay --oplogLimit=1696477032 --oplogFile=/home/mongodb/dump/local/oplog.rs.bson /home/mongodb/dump/
```

## 샤드클러스터 구성일 경우

---

Oplog 은 Replica set 에만 존재하기 때문에 MongoS 를 통해 작업이 진행되는 샤드클러스터 구성일 경우는 Oplog 확인 어렵습니다.
Oplog 를 확인하기 위해서는 각각 s1/s2 로 구성되어 있다면 각각의 Replica set 에서만 확인이 가능하며
위와 같이 oplog 를 가져와 MongoS 에서 진행할 경우 제대로 복원이 되지 않습니다.

샤딩되어 있는 특정 컬렉션에서 이슈가 발생하였을 때 복원하려면?

1. 다른 인스턴스에 s1 와 s2 의 데이터 백업본을 복원을 진행

2. 각각의 s1/s2 에서 Oplog 에서 특정 컬렉션 관련 추출
```
s1/s2 각각에서 추출
mongodump --host <host> --username <user> --password <pwd> --authenticationDatabase admin -d local -c oplog.rs --query '{"ns":"DB명.컬렉션명"}' -o /home/mongodb/dump/
```

3. 각각의 s1/s2 의 Oplog 에서 돌리고자 하는 시점 확인
```
{
	"ts": Timestamp(1696489608, 1),
	"t": NumberLong(1),
	"h": NumberLong("-6473836767184110140"),
	"v": 2,
	"op": "d",
	"ns": "ME_SPARTY.USER_INFO_M",
	"ui": UUID("7aa1b5ed-962c-4d41-bad1-eb7cb6dc866f"),
	"wall": ISODate("2023-10-05T07:06:48.674Z"),
	"o": {
		"_id": ObjectId("5982c2dd43b2f9980a371a62")
	}
}
```

4. 다른 인스턴스에 각각 s1/s2 백업본 데이터에 추출한 oplog 를 통해서 복구 진행
```
mongorestore --host <host> --username <user> --password <pwd> --authenticationDatabase admin --oplogReplay --oplogLimit=1696489608 --oplogFile=/home/mongodb/dump/local/oplog.rs.bson /home/mongodb/dump/
```

5. 복구가 완료된 s1/s2 컬렉션을 각각 export 진행
```
mongodump --host <host> --username <user> --password <pwd> --authenticationDatabase=admin --db DB명 --collection 컬렉션명 --gzip --out /home/mongodb/mongodb_backup/s1
```

6. export 완료된 데이터를 라이브 환경 MongoS 를 통해 import 진행
```
mongorestore --host <host> --username <user> --password <pwd> --authenticationDatabase admin  --gzip --db DB명 --collection 컬렉션명_V2 /home/mongodb/mongodb_backup/s1/DB명/컬렉션명.bson.gz
```


### 정리

---
DB 에 롤백이 필요할 경우 Oplog 가 있다면 여러 필요 옵션들을 통해서 설정이 가능합니다.
하지만 Oplog 를 가지고 있다고 해서 모든 것으로 복원할 수 있는 것은 아닙니다.
Oplog 는 저희가 설정해놓은 용량만큼만 가지고 있기 때문에 만약 이틀치의 Oplog 를 가지고 있고,
이전 상태의 백업 데이터가 1주일 전의 데이터라면 이는 롤백이 불가능합니다.
백업 정책과 Oplog 가 며칠분의 operations 를 들고 있는지 등의 확인이 항상 필요합니다.