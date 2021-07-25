---
description: 'Shard 추가 및 Hash, Range shard key 구성'
---

# 3. Sharding 구성

## Shard 서버 추가

mongos 접속 후 shard 서버를 추가

sh.addShard\("replicaset name/host:port"\);

```bash

mongos> sh.addShard("localhost:30001")
{
        "shardAdded" : "shard0000",
        "ok" : 1,
        "operationTime" : Timestamp(1627192520, 4),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627192520, 4),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
mongos> sh.addShard("localhost:30002")
{
        "shardAdded" : "shard0001",
        "ok" : 1,
        "operationTime" : Timestamp(1627192527, 3),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627192527, 3),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
mongos> sh.addShard("localhost:30003")
{
        "shardAdded" : "shard0002",
        "ok" : 1,
        "operationTime" : Timestamp(1627192532, 4),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627192532, 4),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
```

{% hint style="info" %}
 샤드 서버 구성시 replica set을 구성하지 않았으므로 host명 앞에 replica set은 명시하지 않음 
{% endhint %}

샤드 추가 후 sh.status\(\)로 구성 확인

```bash
mongos> sh.status()
--- Sharding Status ---
  sharding version: {
        "_id" : 1,
        "minCompatibleVersion" : 5,
        "currentVersion" : 6,
        "clusterId" : ObjectId("60fcf3231e2350cd9dcce677")
  }
  shards:
        {  "_id" : "shard0000",  "host" : "localhost:30001",  "state" : 1 }
        {  "_id" : "shard0001",  "host" : "localhost:30002",  "state" : 1 }
        {  "_id" : "shard0002",  "host" : "localhost:30003",  "state" : 1 }
  active mongoses:
        "4.2.15" : 1
  autosplit:
        Currently enabled: yes
  balancer:
        Currently enabled:  yes
        Currently running:  no
        Failed balancer rounds in last 5 attempts:  0
        Migration Results for the last 24 hours:
                39 : Success
  databases:
        {  "_id" : "config",  "primary" : "config",  "partitioned" : true }
                config.system.sessions
                        shard key: { "_id" : 1 }
                        unique: false
                        balancing: true
                        chunks:
                                shard0000       985
                                shard0001       20
                                shard0002       19
                        too many chunks to print, use verbose if you want to force print
```

## Shard Database 및 Collection 샤드키 구성

### Hashed Shard

hashdb에 샤딩 활성화\(Database를 미리 생성하지 않아도 \)

```bash
mongos> sh.enableSharding("hashdb")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627192794, 11),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627192794, 11),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
```

{% hint style="info" %}
데이터가 존재하는 collection의 샤딩 절차와  빈 collection의 샤딩 절차가 다름

테스트 진행에는 신규 Database를 생성하고 빈 collection으로 진행 

데이터가 존재하는 collection의 경우 샤딩키 선택과 인덱스를 적절하게 적용해 주어야함

\(이 부분은 추후 검토\)
{% endhint %}

활성화 된 hashdb에 coll이라는 collection의 샤딩키를 hash 키로 지

```bash
mongos> sh.shardCollection("hashdb.coll",{_id : "hashed"})
{
        "collectionsharded" : "hashdb.coll",
        "collectionUUID" : UUID("5fc3a2f8-bd1d-4261-beab-0c07420e5004"),
        "ok" : 1,
        "operationTime" : Timestamp(1627193254, 22),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627193254, 22),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
```

샤드키 지정 후 sh.status\(\)로 샤딩 상태 확인

```bash
mongos> sh.status()
--- Sharding Status ---
  sharding version: {
        "_id" : 1,
        "minCompatibleVersion" : 5,
        "currentVersion" : 6,
        "clusterId" : ObjectId("60fcf3231e2350cd9dcce677")
  }
  shards:
        {  "_id" : "shard0000",  "host" : "localhost:30001",  "state" : 1 }
        {  "_id" : "shard0001",  "host" : "localhost:30002",  "state" : 1 }
        {  "_id" : "shard0002",  "host" : "localhost:30003",  "state" : 1 }
  active mongoses:
        "4.2.15" : 1
  autosplit:
        Currently enabled: yes
  balancer:
        Currently enabled:  yes
        Currently running:  yes
        Failed balancer rounds in last 5 attempts:  0
        Migration Results for the last 24 hours:
                682 : Success
  databases:
        {  "_id" : "config",  "primary" : "config",  "partitioned" : true }
                config.system.sessions
                        shard key: { "_id" : 1 }
                        unique: false
                        balancing: true
                        chunks:
                                shard0000       342
                                shard0001       341
                                shard0002       341
                        too many chunks to print, use verbose if you want to force print
        {  "_id" : "hashdb",  "primary" : "shard0001",  "partitioned" : true,  "version" : {  "uuid" : UUID("3fc504b5-3fc7-4028-97ef-1d2f54c5847e"),  "lastMod" : 1 } }
                hashdb.coll
                        shard key: { "_id" : "hashed" }
                        unique: false
                        balancing: true
                        chunks:
                                shard0000       2
                                shard0001       2
                                shard0002       2
                        { "_id" : { "$minKey" : 1 } } -->> { "_id" : NumberLong("-6148914691236517204") } on : shard0000 Timestamp(1, 0)
                        { "_id" : NumberLong("-6148914691236517204") } -->> { "_id" : NumberLong("-3074457345618258602") } on : shard0000 Timestamp(1, 1)
                        { "_id" : NumberLong("-3074457345618258602") } -->> { "_id" : NumberLong(0) } on : shard0001 Timestamp(1, 2)
                        { "_id" : NumberLong(0) } -->> { "_id" : NumberLong("3074457345618258602") } on : shard0001 Timestamp(1, 3)
                        { "_id" : NumberLong("3074457345618258602") } -->> { "_id" : NumberLong("6148914691236517204") } on : shard0002 Timestamp(1, 4)
                        { "_id" : NumberLong("6148914691236517204") } -->> { "_id" : { "$maxKey" : 1 } } on : shard0002 Timestamp(1, 5)

```

### hashdb Database collection에 데이터 입력 테스트 

```bash

mongos> use hashdb
switched to db hashdb
mongos> db
hashdb
mongos> db.coll.insert({ name : "yoo" , dept : "data group", status : "a"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "kim" , dept : "data group", status : "b"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "lee" , dept : "data group", status : "c"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "park" , dept : "data group", status : "d"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "choi" , dept : "data group", status : "e"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "aaa" , dept : "data group", status : "f"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "bbb" , dept : "data group", status : "g"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "ccc" , dept : "data group", status : "h"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "ddd" , dept : "data group", status : "i"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "eee" , dept : "data group", status : "j"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "fff" , dept : "data group", status : "k"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "ggg" , dept : "data group", status : "l"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({ name : "ggg" , dept : "data group", status : "l"})
WriteResult({ "nInserted" : 1 })
#단일데이터로 넣어보며 3개의 shard 서버에 적절하게 들어가는지 테스트 하기위해 아래 for문 실
mongos> for(var i=0;i<100;i++){db.coll.insert({name:"abc"+i})}


```

### Shard 서버 접속 후 데이터 확인

```bash
#mongos 라우터 서버에서 전체 카운트 113건
mongos> use hashdb
switched to db hashdb
mongos> db.coll.count()
113
mongos> exit
bye

##각 shard 서버에 접속 후 데이터 확인

#port 30001 서버 
C:\Users\clooadmin>mongo --port 30001
> use hashdb
switched to db hashdb
> db.coll.count()
38
> exit
bye

#port 30002 서버 
C:\Users\clooadmin>mongo --port 30002
> use hashdb
switched to db hashdb
> db.coll.count()
36
> exit
bye

#port 30003 서버 
C:\Users\clooadmin>mongo --port 30003
> use hashdb
switched to db hashdb
> db.coll.count()
39

```

각 shard 서버에 hashed 키로 데이터가 적절하게 분배되어 입력됨

### Ranged Shard

ranged에 샤딩 활성화\(Database를 미리 생성하지 않아도 됨\)

```bash
mongos> sh.enableSharding("ranged")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627195458, 4),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195458, 4),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}

```

활성화 된 ranged에 coll이라는 collection의 샤딩키를 range 키로 지

```bash
mongos> sh.shardCollection("ranged.coll", {no: 1})
{
        "collectionsharded" : "ranged.coll",
        "collectionUUID" : UUID("d8dea1d4-0ef9-4088-87b1-b0bdb7afe4b0"),
        "ok" : 1,
        "operationTime" : Timestamp(1627195573, 8),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195573, 8),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
```

hash 키와 다르게 range 키는 샤드 zone 구성 및 key 범위를 지정해 주어야 

sh.addSahrdTag, sh.addShardToZone, sh.updateZoneKeyRange를 이용해 구성

```bash
#shard0000을 zone1로 태그지
mongos> sh.addShardTag("shard0000","zone1")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627195638, 1),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195638, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
#shard0001을 zone2로 태그지
mongos> sh.addShardTag("shard0001","zone2")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627195681, 2),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195681, 2),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
#shard0002을 zone3로 태그지
mongos> sh.addShardTag("shard0002","zone3")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627195698, 1),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195698, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
```

```bash
#지정한 태그에 샤드 추가 shard0000 to zone1
mongos> sh.addShardToZone("shard0000","zone1")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627195751, 1),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195751, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
#지정한 태그에 샤드 추가 shard0001 to zone2
mongos> sh.addShardToZone("shard0001","zone2")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627195763, 1),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195763, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
#지정한 태그에 샤드 추가 shard0002 to zone3
mongos> sh.addShardToZone("shard0002","zone3")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627195770, 1),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195770, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
```

```bash
#zone1의 샤드키 범위 지정 no 필드를 0~500 까
mongos> sh.updateZoneKeyRange("ranged.coll",{no:"0"},{no:"500"},"zone1")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627195905, 1),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195905, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
#zone2의 샤드키 범위 지정 no 필드를 500~600 까
mongos> sh.updateZoneKeyRange("ranged.coll",{no:"500"},{no:"600"},"zone2")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627196213, 1),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627196213, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
#zone3의 샤드키 범위 지정 no 필드를 600~900 까
mongos> sh.updateZoneKeyRange("ranged.coll",{no:"600"},{no:"900"},"zone3")
{
        "ok" : 1,
        "operationTime" : Timestamp(1627196255, 1),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627196255, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
```

{% hint style="info" %}
range 범위 업데이트시 이상하게 1000이상 숫자 들어가면 아래와 같은 오류가 남 추후 확인 
{% endhint %}

```bash
mongos> sh.updateZoneKeyRange("ranged.coll",{no:"500"},{no:"1000"},"zone2")
{
        "ok" : 0,
        "errmsg" : "min: { no: \"500\" } should be less than max: { no: \"1000\" }",
        "code" : 9,
        "codeName" : "FailedToParse",
        "operationTime" : Timestamp(1627195921, 1),
        "$clusterTime" : {
                "clusterTime" : Timestamp(1627195921, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
```

범위 업데이트가 끝나면 sh.status\(\)로 샤딩키 범위 및 상태 확인 

```bash
mongos> sh.status()
--- Sharding Status ---
  sharding version: {
        "_id" : 1,
        "minCompatibleVersion" : 5,
        "currentVersion" : 6,
        "clusterId" : ObjectId("60fcf3231e2350cd9dcce677")
  }
  shards:
        {  "_id" : "shard0000",  "host" : "localhost:30001",  "state" : 1,  "tags" : [ "zone1" ] }
        {  "_id" : "shard0001",  "host" : "localhost:30002",  "state" : 1,  "tags" : [ "zone2" ] }
        {  "_id" : "shard0002",  "host" : "localhost:30003",  "state" : 1,  "tags" : [ "zone3" ] }
  active mongoses:
        "4.2.15" : 1
  autosplit:
        Currently enabled: yes
  balancer:
        Currently enabled:  yes
        Currently running:  no
        Failed balancer rounds in last 5 attempts:  0
        Migration Results for the last 24 hours:
                685 : Success
  databases:
        {  "_id" : "config",  "primary" : "config",  "partitioned" : true }
                config.system.sessions
                        shard key: { "_id" : 1 }
                        unique: false
                        balancing: true
                        chunks:
                                shard0000       342
                                shard0001       341
                                shard0002       341
                        too many chunks to print, use verbose if you want to force print
        {  "_id" : "hashdb",  "primary" : "shard0001",  "partitioned" : true,  "version" : {  "uuid" : UUID("3fc504b5-3fc7-4028-97ef-1d2f54c5847e"),  "lastMod" : 1 } }
                hashdb.coll
                        shard key: { "_id" : "hashed" }
                        unique: false
                        balancing: true
                        chunks:
                                shard0000       2
                                shard0001       2
                                shard0002       2
                        { "_id" : { "$minKey" : 1 } } -->> { "_id" : NumberLong("-6148914691236517204") } on : shard0000 Timestamp(1, 0)
                        { "_id" : NumberLong("-6148914691236517204") } -->> { "_id" : NumberLong("-3074457345618258602") } on : shard0000 Timestamp(1, 1)
                        { "_id" : NumberLong("-3074457345618258602") } -->> { "_id" : NumberLong(0) } on : shard0001 Timestamp(1, 2)
                        { "_id" : NumberLong(0) } -->> { "_id" : NumberLong("3074457345618258602") } on : shard0001 Timestamp(1, 3)
                        { "_id" : NumberLong("3074457345618258602") } -->> { "_id" : NumberLong("6148914691236517204") } on : shard0002 Timestamp(1, 4)
                        { "_id" : NumberLong("6148914691236517204") } -->> { "_id" : { "$maxKey" : 1 } } on : shard0002 Timestamp(1, 5)
        {  "_id" : "ranged",  "primary" : "shard0000",  "partitioned" : true,  "version" : {  "uuid" : UUID("d81c3ff9-06e3-435e-99ad-e5ad31c9ca41"),  "lastMod" : 1 } }
                ranged.coll
                        shard key: { "no" : 1 }
                        unique: false
                        balancing: true
                        chunks:
                                shard0000       2
                                shard0001       1
                                shard0002       2
                        { "no" : { "$minKey" : 1 } } -->> { "no" : "0" } on : shard0002 Timestamp(2, 0)
                        { "no" : "0" } -->> { "no" : "500" } on : shard0000 Timestamp(4, 1)
                        { "no" : "500" } -->> { "no" : "600" } on : shard0001 Timestamp(3, 0)
                        { "no" : "600" } -->> { "no" : "900" } on : shard0002 Timestamp(4, 0)
                        { "no" : "900" } -->> { "no" : { "$maxKey" : 1 } } on : shard0000 Timestamp(3, 3)
                         tag: zone1  { "no" : "0" } -->> { "no" : "500" }
                         tag: zone2  { "no" : "500" } -->> { "no" : "600" }
                         tag: zone3  { "no" : "600" } -->> { "no" : "900" }
```

### Ranged Database collection에 데이터 입력 테스트

```bash
mongos> use ranged
switched to db ranged
mongos> db
ranged
mongos> db.coll.insert({no:"1", name : "aaa"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({no:"550", name : "bbb"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.insert({no:"850", name : "ccc"})
WriteResult({ "nInserted" : 1 })
mongos> db.coll.find()
{ "_id" : ObjectId("60fd0bf44a6c5f0f8c3cf355"), "no" : "1", "name" : "aaa" }
{ "_id" : ObjectId("60fd0c264a6c5f0f8c3cf356"), "no" : "550", "name" : "bbb" }
{ "_id" : ObjectId("60fd0c534a6c5f0f8c3cf357"), "no" : "850", "name" : "ccc" }
mongos> exit
bye

#port 30001 shard 서버 접
C:\Users\clooadmin>mongo --port 30001
> use ranged
switched to db ranged
> db.coll.find()
{ "_id" : ObjectId("60fd0bf44a6c5f0f8c3cf355"), "no" : "1", "name" : "aaa" }
> exit
bye

#port 30002 shard 서버 접속 
C:\Users\clooadmin>mongo --port 30002
> use ranged
switched to db ranged
> db
ranged
> db.coll.find()
{ "_id" : ObjectId("60fd0c264a6c5f0f8c3cf356"), "no" : "550", "name" : "bbb" }
> exit
bye

#port 30003 shard 서버 접속 
C:\Users\clooadmin>mongo --port 30003
> use ranged
switched to db ranged
> db
ranged
> db.coll.find()
{ "_id" : ObjectId("60fd0c534a6c5f0f8c3cf357"), "no" : "850", "name" : "ccc" }

```

각 설정해둔 range key 범위에 알맞게 들어

