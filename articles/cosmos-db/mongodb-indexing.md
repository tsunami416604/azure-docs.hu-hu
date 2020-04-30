---
title: Az indexelés kezelése Azure Cosmos DB API-MongoDB
description: Ez a cikk áttekintést nyújt Azure Cosmos DB indexelési képességeiről a MongoDB API használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732706"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Az indexelés kezelése Azure Cosmos DB API-MongoDB

A MongoDB Azure Cosmos DB API-ját kihasználhatja Azure Cosmos DB alapvető index-felügyeleti képességeinek kihasználásához. Ez a cikk azt ismerteti, hogyan adhat hozzá indexeket a Azure Cosmos DB API-MongoDB való használatával. Áttekintheti a [Azure Cosmos db indexelésének áttekintését](index-overview.md) is, amely az összes API-ra vonatkozik.

## <a name="indexing-for-mongodb-server-version-36"></a>A MongoDB Server 3,6-es verziójának indexelése

A MongoDB Server 3,6-es verziójának Azure Cosmos DB API-je `_id` automatikusan indexeli a mezőt, amely nem lehet eldobni. Automatikusan kikényszeríti a `_id` mező egyediségét.

További mezők indexeléséhez alkalmazza a MongoDB index-Management parancsait. Ahogy a MongoDB-ben, Azure Cosmos DB API-ját a MongoDB automatikusan `_id` indexeli a mezőt. Ez az alapértelmezett indexelési házirend eltér a Azure Cosmos DB SQL API-tól, amely alapértelmezés szerint indexeli az összes mezőt.

A rendezés lekérdezésre való alkalmazásához létre kell hoznia egy indexet a rendezési műveletben használt mezőkön.

## <a name="index-types"></a>Indextípusok

### <a name="single-field"></a>Egyetlen mező

Létrehozhat indexeket bármely egyetlen mezőben. Az egymezős index rendezési sorrendje nem számít. A következő parancs létrehoz egy indexet a mezőhöz `name`:

`db.coll.createIndex({name:1})`

Az egyik lekérdezés több egymezős indexet használ, ahol elérhető. Egy tárolón legfeljebb 500 egymezős index hozható létre.

### <a name="compound-indexes-mongodb-server-version-36"></a>Összetett indexek (MongoDB-kiszolgáló 3,6-es verzió)

A MongoDB Azure Cosmos DB API-ját a 3,6-os átviteli protokollt használó fiókok összetett indexeit támogatja. Akár nyolc mezőt is hozzáadhat egy összetett indexben. A MongoDB eltérően csak akkor érdemes összetett indexet létrehozni, ha a lekérdezésnek egyszerre több mezőn kell rendeznie. Több olyan szűrővel rendelkező lekérdezések esetén, amelyek nem szükségesek a rendezéshez, egyetlen összetett index helyett hozzon létre több egymezős indexet.

A következő parancs létrehoz egy összetett indexet a `name` mezőkön `age`, és:

`db.coll.createIndex({name:1,age:1})`

Az összetett indexek segítségével egyszerre több mezőn rendezheti a műveleteket, ahogy az az alábbi példában is látható:

`db.coll.find().sort({name:1,age:1})`

Az előző összetett index használatával a lekérdezéseket az összes mező ellentétes rendezési sorrendjével is hatékonyan rendezheti. Például:

`db.coll.find().sort({name:-1,age:-1})`

Az összetett indexben lévő elérési utak sorrendjének azonban pontosan egyeznie kell a lekérdezéssel. Az alábbi példa egy olyan lekérdezést mutat be, amely további összetett indexet igényel:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Multikey indexek

A Azure Cosmos DB multikey indexeket hoz létre a tömbökben tárolt tartalom indexeléséhez. Ha Array értéket tartalmazó mezőt indexel, Azure Cosmos DB automatikusan indexeli a tömb minden elemét.

### <a name="geospatial-indexes"></a>Térinformatikai indexek

Számos térinformatikai operátor kihasználja a térinformatikai indexeket. Jelenleg Azure Cosmos DB API-MongoDB támogatja `2dsphere` az indexeket. Az API még nem támogatja `2d` az indexeket.

Az alábbi példa egy térinformatikai index létrehozását szemlélteti a `location` mezőn:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Szöveges indexek

Azure Cosmos DB API-MongoDB jelenleg nem támogatja a szöveges indexeket. A karakterláncokra vonatkozó szöveges keresési lekérdezésekhez az [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) integrációját kell használnia Azure Cosmos DBokkal.

## <a name="index-properties"></a>Index tulajdonságai

A következő műveletek közösek a 3,6-es és korábbi verziókat kiszolgáló fiókok esetében. További információ a [támogatott indexekről és az indexelt tulajdonságokról](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Egyedi indexek

Az [egyedi indexek](unique-keys.md) akkor hasznosak, ha a két vagy több dokumentum nem tartalmaz azonos értéket az indexelt mezőkhöz.

> [!IMPORTANT]
> Egyedi indexek csak akkor hozhatók létre, ha a gyűjtemény üres (nem tartalmaz dokumentumokat).

A következő parancs létrehoz egy egyedi indexet a mezőhöz `student_id`:

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

A többrészes gyűjtemények esetében meg kell adnia a szegmens (partíció) kulcsot egy egyedi index létrehozásához. Ez azt jelenti, hogy a megosztott gyűjteményekben esetében az összes egyedi index összetett index, és az egyik mező a partíciókulcs.

A következő ```coll``` parancsok a mezőkben ```university``` `student_id` egyedi indextel (a szegmens kulcs) hoznak létre egy többszegmenses gyűjteményt `university`(a Szilánk kulcsa):

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

Az előző példában a ```"university":1``` záradék kihagyása hibaüzenetet ad vissza a következő üzenettel:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL-indexek

Ha engedélyezni szeretné a dokumentumok lejáratát egy adott gyűjteményben, létre kell hoznia egy [élettartam (TTL) indexet](../cosmos-db/time-to-live.md). A TTL index egy `_ts` `expireAfterSeconds` értékkel rendelkező mező indexe.

Példa:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Az előző parancs törli a ```db.coll``` gyűjteményben lévő összes olyan dokumentumot, amely nem lett módosítva az elmúlt 10 másodpercben.

> [!NOTE]
> A **_ts** mező a Azure Cosmos DBra vonatkozik, és nem érhető el a MongoDB-ügyfelektől. Ez egy fenntartott (rendszer) tulajdonság, amely a dokumentum utolsó módosításának időbélyegzőjét tartalmazza.

## <a name="track-index-progress"></a>Index előrehaladásának nyomon követése

A Azure Cosmos DB API-MongoDB 3,6-es verziója támogatja `currentOp()` az index előrehaladásának nyomon követésére szolgáló parancsot az adatbázis-példányon. Ez a parancs egy olyan dokumentumot ad vissza, amely az adatbázis-példányon végzett folyamatban lévő műveletekkel kapcsolatos információkat tartalmaz. A `currentOp` paranccsal nyomon követheti az összes folyamatban lévő műveletet a natív MongoDB. Azure Cosmos DB API-MongoDB ez a parancs csak az indexelési művelet nyomon követését támogatja.

Az alábbi példák azt mutatják be, hogyan használható a `currentOp` parancs az index előrehaladásának nyomon követéséhez:

* Az index előrehaladásának beolvasása egy gyűjteménynél:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Az index előrehaladásának beolvasása az adatbázis összes gyűjteménye esetében:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Az index előrehaladásának beolvasása az Azure Cosmos-fiókban található összes adatbázis és gyűjtemény esetében:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Példa az index folyamatjelző kimenetére

Az index előrehaladásának részletei az aktuális indexelési művelet előrehaladásának százalékos arányát mutatják. Az alábbi példa bemutatja a kimeneti dokumentum formátumát az index különböző szakaszaiban:

- A "foo" gyűjtemény és a "sáv" adatbázisának 60 százalékos készültségű index-művelete a következő kimeneti dokumentummal fog rendelkezni. A `Inprog[0].progress.total` mező a 100 értéket jeleníti meg célként megadott készültségi százalékként.

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

- Ha egy indexelési művelet csak a "foo" gyűjtemény és a "sáv" adatbázison indult el, a kimeneti dokumentum 0 százalékos előrehaladást eredményezhet, amíg el nem éri a mérhető szintet.

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

- Ha a folyamatban lévő index művelet befejeződik, a kimeneti dokumentum üres `inprog` műveleteket jelenít meg.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Háttérben futó index frissítései

A **háttérbeli** index tulajdonsághoz megadott értéktől függetlenül az index frissítése mindig a háttérben történik. Mivel az index frissítései alacsonyabb prioritással használják a kérelmek egységeit, mint a többi adatbázis-műveletnél, az index módosításai nem eredményeznek semmilyen állásidőt az írási, frissítési és törlési műveletekhez.

Új index hozzáadásakor a lekérdezések azonnal az indexet fogják használni. Ez azt jelenti, hogy előfordulhat, hogy a lekérdezések nem adják vissza az összes egyező eredményt, így a hibák visszaküldése nélkül is megtörténnek. Az index átalakításának befejeződése után a lekérdezés eredményei konzisztensek lesznek. [Nyomon követheti az index előrehaladását](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Gyűjtemények migrálása indexekkel

Jelenleg csak akkor hozhat létre egyedi indexeket, ha a gyűjtemény nem tartalmaz dokumentumokat. A népszerű MongoDB áttelepítési eszközök megpróbálják létrehozni az egyedi indexeket az Adatimportálás után. A probléma megkerüléséhez manuálisan is létrehozhatja a megfelelő gyűjteményeket és egyedi indexeket ahelyett, hogy engedélyezné az áttelepítési eszköz kipróbálását. (Ezt a viselkedést ```mongorestore``` a parancssorban a `--noIndexRestore` jelölő használatával érheti el.)

## <a name="indexing-for-mongodb-version-32"></a>Az MongoDB 3,2-es verziójának indexelése

Az elérhető indexelési funkciók és alapértékek eltérnek az Azure Cosmos-fiókoknál, amelyek kompatibilisek az MongoDB-os 3,2-es verziójával. Megtekintheti [a fiókja verzióját](mongodb-feature-support-36.md#protocol-support). A 3,6-es verzióra való frissítéshez egy [támogatási kérést](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)kell benyújtani.

Ha az 3,2-es verziót használja, ez a szakasz az 3,6-es verzió főbb különbségeit ismerteti.

### <a name="dropping-default-indexes-version-32"></a>Alapértelmezett indexek eldobása (3,2-es verzió)

A MongoDB-hez készült Azure Cosmos DB API 3,6-es verziójától eltérően a 3,2-es verzió alapértelmezés szerint minden tulajdonságot indexel. A következő parancs használatával elhúzhatja ezeket az alapértelmezett indexeket egy gyűjteményhez (```coll```):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Az alapértelmezett indexek eldobása után további indexeket adhat hozzá, ahogy az 3,6-es verzióban.

### <a name="compound-indexes-version-32"></a>Összetett indexek (3,2-es verzió)

Az összetett indexek egy dokumentum több mezőjére vonatkozó hivatkozásokat tartalmaznak. Ha összetett indexet szeretne létrehozni, frissítsen a 3,6-es verzióra egy [támogatási kérelem](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)bejelentésével.

## <a name="next-steps"></a>További lépések

* [Indexelés az Azure Cosmos DB-ben](../cosmos-db/index-policy.md)
* [Az Azure Cosmos DB automatikusan lejár az idő az élettartammal](../cosmos-db/time-to-live.md)
