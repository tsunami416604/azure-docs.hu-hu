---
title: Indexelés az Azure Cosmos DB API a mongodb-hez
description: Megadja az indexelési képességek áttekintése az Azure Cosmos DB API a mongodb-hez.
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: 4c6ba4b12625433f0b0218dc88decf2d7cb7ad77
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792282"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexelés az Azure Cosmos DB API használatával a mongodb-hez

Az Azure Cosmos DB MongoDB API-automatikus Indexkezelési funkcióit Cosmos DB használja. Ennek eredményeképpen a felhasználók alapértelmezett indexelési szabályzataihoz Cosmos DB hozzáféréssel rendelkeznek. Tehát ha nincsenek indexek a felhasználó által meghatározott, vagy nincsenek indexek el lett dobva, majd összes mezőt indexeli, amikor beszúrja a gyűjtemény alapértelmezés szerint. A legtöbb esetben ajánlott a fiókhoz beállított alapértelmezett indexelési szabályzat használata.

## <a name="dropping-the-default-indexes"></a>Az alapértelmezett indexek elvetése

A következő paranccsal elvethetők a ```coll``` nevű gyűjteményhez tartozó alapértelmezett indexek:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>Összetett indexek létrehozása

Az összetett indexek egy dokumentum több mezőjére vonatkozó hivatkozásokat tartalmaznak. Logikailag ez egyenértékű azzal, mint ha mezőnként több különálló indexet hozna létre. A Cosmos DB indexelési technikáival elérhető optimalizálások előnyeinek kiaknázása érdekében az egyetlen (nem egyedi), összetett index helyett inkább több különálló index létrehozását javasoljuk.

## <a name="creating-unique-indexes"></a>Egyedi indexek létrehozása

Az [egyedi indexek](unique-keys.md) hasznosak annak biztosítására, hogy két vagy több dokumentum ne tartalmazhassa ugyanazt az értéket az indexelt mezőkben. 
>[!important] 
> Egyedi indexeket jelenleg csak akkor lehet létrehozni, ha a gyűjtemény üres (nem tartalmaz dokumentumokat). 

A következő parancs létrehoz egy egyedi index "student_id" mező:

```JavaScript
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

```JavaScript
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
>

## <a name="migrating-collections-with-indexes"></a>Indexekkel rendelkező gyűjtemények migrálása

Egyedi indexek jelenleg csak akkor hozhatók létre, ha a gyűjtemény nem tartalmaz dokumentumokat. A MongoDB népszerű migrálási eszközei az adatok importálása után megpróbálják létrehozni az egyedi indexeket. A probléma megkerülése értekében a felhasználóknak érdemes manuálisan létrehozniuk a megfelelő gyűjteményeket vagy egyedi indexeket ahelyett, hogy a migrálási eszköznek engedélyeznék ezt (a ```mongorestore``` esetében ez a viselkedés a parancssorba illesztett --noIndexRestore jelző használatával érhető el).

## <a name="next-steps"></a>További lépések
* [Az Azure Cosmos DB indexelése](../cosmos-db/index-policy.md)
* [Adatok az Azure Cosmos DB automatikusan az élettartam elévülése](../cosmos-db/time-to-live.md)
