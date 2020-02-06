---
title: Indexelés Azure Cosmos DB API-MongoDB
description: Áttekintést nyújt az indexelési lehetőségekről Azure Cosmos DB API-MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c8879884cf3d882e6a6b441244ed139072bedeeb
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029469"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexelés a MongoDB Azure Cosmos DB API-ját használva

A MongoDB Azure Cosmos DB API-ját a Cosmos DB automatikus indexelési képességeinek kihasználására használják. Ennek eredményeképpen a felhasználók hozzáférhetnek Cosmos DB alapértelmezett indexelési házirendjeihez. Így ha a felhasználó nem adott meg indexeket, vagy nem lettek eltávolítva indexek, a rendszer alapértelmezés szerint automatikusan indexeli az összes mezőt a gyűjteménybe való beillesztéskor. A legtöbb esetben ajánlott a fiókhoz beállított alapértelmezett indexelési szabályzat használata.

## <a name="indexing-for-version-36"></a>Az 3,6-es verzió indexelése

Az 3,6-es átviteli protokoll verzióját kiszolgáló fiókok eltérő alapértelmezett indexelési házirendet biztosítanak, mint a korábbi verziók által biztosított házirend. Alapértelmezés szerint csak a _id mező van indexelve. További mezők indexeléséhez a felhasználónak alkalmaznia kell a MongoDB index felügyeleti parancsait. Ha a rendezést egy lekérdezésre szeretné alkalmazni, a rendezési műveletben használt mezőkön jelenleg indexet kell létrehozni.

### <a name="dropping-the-default-indexes-36"></a>Az alapértelmezett indexek eldobása (3,6)

Az 3,6-es átviteli protokollt kiszolgáló fiókok esetében az egyetlen alapértelmezett index _id, amely nem lehet eldobni.

### <a name="creating-a-compound-index-36"></a>Összetett index létrehozása (3,6)

Az 3,6-os vezetékes protokollt használó fiókok esetében az igaz összetett indexek támogatottak. A következő parancs létrehoz egy összetett indexet az "a" és a "b" mezőknél: `db.coll.createIndex({a:1,b:1})`

Az összetett indexek hatékonyan rendezhetők egyszerre több mezőn, például a következő módon: `db.coll.find().sort({a:1,b:1})`

### <a name="track-the-index-progress"></a>Az index előrehaladásának nyomon követése

Azure Cosmos DB API-MongoDB-fiókjainak 3,6-es verziója támogatja az `currentOp()` parancsot az index előrehaladásának nyomon követéséhez egy adatbázis-példányon. Ez a parancs egy olyan dokumentumot ad vissza, amely egy adatbázis-példányon végzett folyamatban lévő műveletekkel kapcsolatos információkat tartalmaz. A `currentOp` parancs a natív MongoDB lévő összes folyamatban lévő művelet nyomon követésére szolgál, míg a Azure Cosmos DB API-MongoDB a parancs csak az indexelési művelet nyomon követését támogatja.

Íme néhány példa, amely bemutatja, hogyan használhatja a `currentOp` parancsot az index előrehaladásának nyomon követéséhez:

• Az index előrehaladásának beolvasása egy gyűjteménynél:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

• Az index előrehaladásának beolvasása az adatbázis összes gyűjteményéhez:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

• Az Azure Cosmos-fiókban lévő összes adatbázis és gyűjtemény index-előrehaladásának beolvasása:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Az index előrehaladásának részletei az aktuális index művelethez tartozó előrehaladás százalékos arányát tartalmazzák. Az alábbi példa a kimeneti dokumentum formátumát mutatja be az index különböző szakaszaiban:

1. Ha az indexelési művelet egy "foo" gyűjtemény és "Bar" adatbázison, amelynek 60%-os indexelése befejeződött, a következő kimeneti dokumentum fog megjelenni. `Inprog[0].progress.total` az 100-as a cél befejezését mutatja.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. A "foo" gyűjtemény és a "Bar" adatbázison éppen elindított indexelési művelet esetén a kimeneti dokumentum 0%-os előrehaladást eredményezhet, amíg nem ér el mérhető szintre.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. Ha a folyamatban lévő index művelet befejeződik, a kimeneti dokumentum üres inprog műveleteket jelenít meg.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="indexing-for-version-32"></a>Az 3,2-es verzió indexelése

### <a name="dropping-the-default-indexes-32"></a>Az alapértelmezett indexek eldobása (3,2)

A következő paranccsal elvethetők a ```coll``` nevű gyűjteményhez tartozó alapértelmezett indexek:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Összetett index létrehozása (3,2)

Az összetett indexek egy dokumentum több mezőjére vonatkozó hivatkozásokat tartalmaznak. Logikailag ez egyenértékű azzal, mint ha mezőnként több különálló indexet hozna létre. A Cosmos DB indexelési technikáival elérhető optimalizálások előnyeinek kiaknázása érdekében az egyetlen (nem egyedi), összetett index helyett inkább több különálló index létrehozását javasoljuk.

## <a name="common-indexing-operations"></a>Gyakori indexelési műveletek

A következő műveletek közösek mind a 3,6-es, mind a huzal protokoll verzióját kiszolgáló fiókok esetében. 

## <a name="creating-unique-indexes"></a>Egyedi indexek létrehozása

Az [egyedi indexek](unique-keys.md) hasznosak annak biztosítására, hogy két vagy több dokumentum ne tartalmazhassa ugyanazt az értéket az indexelt mezőkben.

>[!Important]
> Egyedi indexeket jelenleg csak akkor lehet létrehozni, ha a gyűjtemény üres (nem tartalmaz dokumentumokat).

A következő parancs egy egyedi indexet hoz létre a (z) "student_id" mezőhöz:

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

A horizontálisan skálázott gyűjteményekben a MongoDB-viselkedés szerint az egyedi index létrehozásához meg kell adnia a szegmenskulcsot (partíciókulcsot). Ez azt jelenti, hogy a megosztott gyűjteményekben esetében az összes egyedi index összetett index, és az egyik mező a partíciókulcs.

Az alábbi parancsok egy ```coll``` nevű megosztott gyűjteményt hoznak létre (a szegmenskulcs: ```university```), a student_id és az university mezőket tartalmazó egyedi indexszel:

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Ha a fenti példában kimarad a ```"university":1``` záradék, a következő hibaüzenet jelenik meg:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL-indexek

A dokumentumok lejáratának egy adott gyűjteményben történő engedélyezéséhez [TTL-index (élettartamindex)](../cosmos-db/time-to-live.md) létrehozása szükséges. A TTL-index a _ts mező indexe expireAfterSeconds értékkel.

Példa:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Az előző parancs minden olyan dokumentumot töröl a ```db.coll``` gyűjteményből, amelyet az elmúlt 10 másodpercben nem módosítottak.

> [!NOTE]
> A **_ts** egy Cosmos DB-specifikus mező, amely nem érhető el a MongoDB-ügyfelekből. Ez egy fenntartott (rendszer-) tulajdonság, amely tartalmazza a dokumentum utolsó módosításának időbélyegét.

## <a name="migrating-collections-with-indexes"></a>Indexekkel rendelkező gyűjtemények migrálása

Egyedi indexek jelenleg csak akkor hozhatók létre, ha a gyűjtemény nem tartalmaz dokumentumokat. A MongoDB népszerű migrálási eszközei az adatok importálása után megpróbálják létrehozni az egyedi indexeket. A probléma megkerülése érdekében javasoljuk, hogy a felhasználók manuálisan hozzanak létre a megfelelő gyűjteményeket és egyedi indexeket ahelyett, hogy engedélyezik az áttelepítési eszközt (```mongorestore``` ez a viselkedés a parancssorban a `--noIndexRestore` jelzővel érhető el).

## <a name="next-steps"></a>Következő lépések

* [Indexelés Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Az Azure Cosmos DB automatikusan lejár az idő az élettartammal](../cosmos-db/time-to-live.md)
