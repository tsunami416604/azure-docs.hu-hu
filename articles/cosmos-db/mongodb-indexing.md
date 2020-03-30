---
title: Indexelés az Azure Cosmos DB MongoDB-hoz elérhető API-jában
description: Áttekintést nyújt az indexelési képességek az Azure Cosmos DB MongoDB API-jával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/27/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 7c75f0d6f74fe8cf1417e0dc40a5ad01615d7057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371075"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexelés az Azure Cosmos DB MongoDB-hoz való API-jával

Az Azure Cosmos DB MongoDB-hoz való API-ja kihasználja az Azure Cosmos DB alapvető indexkezelési képességeit. Ez a dokumentum arra összpontosít, hogyan adhat hozzá indexeket az Azure Cosmos DB MongoDB API-jával. Az [Azure Cosmos DB indexelésének áttekintését](index-overview.md) is elolvashatja, amely az összes API-ban releváns.

## <a name="indexing-for-version-36"></a>Indexelés a 3.6-os verzióhoz

A `_id` mező indexelése mindig automatikusan történik, és nem ejthető el. Az Azure Cosmos DB MongoDB API-ja automatikusan kényszeríti a `_id` mező egyediségét szegmenskulcsonként.

További mezők indexeléséhez alkalmazza a MongoDB indexkezelési parancsokat. A MongoDB-hoz `_id` is csak a mező lesz indexelve automatikusan. Ez az alapértelmezett indexelési szabályzat eltér az Azure Cosmos DB SQL API-tól, amely alapértelmezés szerint az összes mezőt indexeli.

Ha egy lekérdezésre rendezést szeretne alkalmazni, indexet kell létrehozni a rendezési műveletben használt mezőkön.

## <a name="index-types"></a>Indextípusok

### <a name="single-field"></a>Egyetlen mező

Indexeket bármely mezőben létrehozhat. Az egymezős index rendezési sorrendje nem számít. A következő parancs indexet hoz `name`létre a mezőben:

`db.coll.createIndex({name:1})`

Egy lekérdezés több egymezős indexet fog használni, ahol elérhető.

### <a name="compound-indexes-36"></a>Összetett indexek (3.6.)

Az összetett indexek a 3.6-os vezetékes protokollt használó fiókok esetében támogatottak. Összetett indexbe legfeljebb 8 mezőt vehet fel. A MongoDB-val ellentétben csak akkor hozzon létre összetett indexet, ha a lekérdezésnek egyszerre több mezőben kell hatékonyan rendeznie. A több szűrővel rendelkező lekérdezések esetében, amelyeket nem kell rendezni, egyetlen összetett index helyett több egymezős indexet kell létrehoznia.

A következő parancs összetett indexet `name` hoz `age`létre a mezőkön és :

`db.coll.createIndex({name:1,age:1})`

Az összetett indexek segítségével egyszerre több mezőt is hatékonyan rendezhet, például:

`db.coll.find().sort({name:1,age:1})`

A fenti összetett index az összes mező ellenkező rendezési sorrendjét tartalmazó lekérdezések hatékony rendezésére is használható. Például:

`db.coll.find().sort({name:-1,age:-1})`

Az összetett indexben lévő görbék sorrendjének azonban pontosan meg kell egyeznie a lekérdezéssel. Íme egy példa egy olyan lekérdezésre, amely további összetett indexet igényel:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Többkulcsos indexek

Az Azure Cosmos DB többkulcsos indexeket hoz létre a tömbökben tárolt tartalom indexeléséhez. Ha egy tömbértékkel rendelkező mezőt indexel, az Azure Cosmos DB automatikusan indexeli a tömb minden elemét.

### <a name="geospatial-indexes"></a>Térinformatikai indexek

Számos térinformatikai operátor számára előnyösek lesznek a térinformatikai indexek. Jelenleg az Azure Cosmos DB MongoDB `2dsphere` API-ja támogatja az indexeket. `2d`indexek még nem támogatottak.

Íme egy példa a térinformatikai index `location` létrehozására a mezőben:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Szövegindexek

A szövegindexek jelenleg nem támogatottak. A karakterláncok szöveges keresési lekérdezések, az [Azure Cognitive Search integrációja az](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) Azure Cosmos DB használatával.

## <a name="index-properties"></a>Index tulajdonságai

A következő műveletek gyakoriak mind a 3.6-os vezetékes protokollverziót kiszolgáló fiókok, mind a korábbi vezetékes protokollverziókat kiszolgáló fiókok esetében. A [támogatott indexekről és az indexelt tulajdonságokról](mongodb-feature-support-36.md#indexes-and-index-properties)is többet tudhat meg.

### <a name="unique-indexes"></a>Egyedi indexek

Az [egyedi indexek](unique-keys.md) hasznosak annak biztosítására, hogy két vagy több dokumentum ne tartalmazhassa ugyanazt az értéket az indexelt mezőkben.

>[!Important]
> Egyedi indexek csak akkor hozhatók létre, ha a gyűjtemény üres (nem tartalmaz dokumentumokat).

A következő parancs egyedi indexet hoz létre a "student_id" mezőben:

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

Szilánkos gyűjtemények létrehozása, egy egyedi index létrehozása megköveteli a szegmens (partíció) kulcs biztosításához. Ez azt jelenti, hogy a megosztott gyűjteményekben esetében az összes egyedi index összetett index, és az egyik mező a partíciókulcs.

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

### <a name="ttl-indexes"></a>TTL-indexek

A dokumentumok lejáratának egy adott gyűjteményben történő engedélyezéséhez [TTL-index (élettartamindex)](../cosmos-db/time-to-live.md) létrehozása szükséges. A TTL-index a _ts mező indexe expireAfterSeconds értékkel.

Példa:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Az előző parancs minden olyan dokumentumot töröl a ```db.coll``` gyűjteményből, amelyet az elmúlt 10 másodpercben nem módosítottak.

> [!NOTE]
> **_ts** egy Azure Cosmos DB-specifikus mező, és nem érhető el a MongoDB-ügyfelektől. Ez egy fenntartott (rendszer-) tulajdonság, amely tartalmazza a dokumentum utolsó módosításának időbélyegét.

## <a name="track-the-index-progress"></a>Az index előrehaladásának nyomon követése

Az Azure Cosmos DB MongoDB-fiókokhoz való API-jának 3.6-os verziója támogatja az `currentOp()` index előrehaladásának nyomon követését egy adatbázis-példányon. Ez a parancs olyan dokumentumot ad vissza, amely az adatbázispéldány folyamatban lévő műveleteire vonatkozó információkat tartalmazza. A `currentOp` parancs a natív MongoDB-ban végzett összes folyamatban lévő művelet nyomon követésére szolgál, míg az Azure Cosmos DB MongoDB API-jában ez a parancs csak az indexművelet nyomon követését támogatja.

Íme néhány példa arra, hogy `currentOp` miként használhatja a parancsot az index előrehaladásának nyomon követésére:

* Gyűjtemény indexének előrehaladása:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Az adatbázis összes gyűjteményének indexállapotának begyűjtése:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Az Azure Cosmos-fiók összes adatbázisának és gyűjteményének indexfolyamatának lekérnie:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Az index előrehaladási részletei az aktuális indexművelet előrehaladásának százalékos arányát tartalmazzák. A következő példa az index állapotának különböző szakaszainak kimeneti dokumentumformátumát mutatja be:

1. Ha a 60 %-os indexelést tartalmazó adatbázis indexműködési művelete a következő kimeneti dokumentummal rendelkezik. `Inprog[0].progress.total`100-at mutat célteljesítésként.

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

2. Egy "foo" gyűjteményen és "sáv" adatbázison éppen megkezdett indexművelet esetén a kimeneti dokumentum 0%-os előrehaladást mutathat, amíg el nem éri a mérhető szintet.

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

3. Amikor a folyamatban lévő index művelet befejeződik, a kimeneti dokumentum üres inprog műveleteket jelenít meg.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Háttérindex frissítései

A **Háttérindex** tulajdonsághoz megadott értéktől függetlenül az indexfrissítések mindig a háttérben történik. Az indexfrissítések alacsonyabb prioritású ru-kat használnak fel, mint más adatbázis-műveletek. Ezért az indexmódosítások nem eredményeznek állásidőt az írások, frissítések vagy törlések esetén.

Új index hozzáadásakor a lekérdezések azonnal felhasználják azt. Ez azt jelenti, hogy a lekérdezések nem adja vissza az összes egyező eredményt, és ezt anélkül, hogy bármilyen hibát. Az index átalakításának befejezése után a lekérdezés iményeredményei konzisztensek lesznek. Nyomon [követheti az index előrehaladását.](#track-the-index-progress)

## <a name="migrating-collections-with-indexes"></a>Indexekkel rendelkező gyűjtemények migrálása

Egyedi indexek jelenleg csak akkor hozhatók létre, ha a gyűjtemény nem tartalmaz dokumentumokat. A MongoDB népszerű migrálási eszközei az adatok importálása után megpróbálják létrehozni az egyedi indexeket. A probléma megkerülése érdekében azt javasoljuk, hogy a felhasználók manuálisan hozzák létre ```mongorestore``` a megfelelő gyűjteményeket és `--noIndexRestore` egyedi indexeket, ahelyett, hogy engedélyeznék az áttelepítési eszközt (ehhez a viselkedéshez a parancssorban lévő jelző használatával érhető el).

## <a name="indexing-for-version-32"></a>Indexelés a 3.2-es verzióhoz

A MongoDB vezetékes protokoll 3.2-es verziójával kompatibilis Azure Cosmos DB-fiókok esetében a rendelkezésre álló indexelési funkciók és alapértelmezések eltérőek. [Ellenőrizheti a fiók verzióját.](mongodb-feature-support-36.md#protocol-support) A 3.6-os verzióra támogatási kérelem benyújtásával [frissíthet.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

A 3.2-es verzió használata esetén ez a szakasz a 3.6-os verzióval való főbb különbségeket ismerteti.

### <a name="dropping-the-default-indexes-32"></a>Az alapértelmezett indexek elvetése (3.2.)

Az Azure Cosmos DB MongoDB-hoz való API-jának 3.6-os verziójával ellentétben a 3.2-es verzió alapértelmezés szerint minden tulajdonságot indexel. A következő paranccsal eldobhatja ezeket ```coll```az alapértelmezett indexeket egy gyűjteményben:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Az alapértelmezett indexek elvetése után további indexeket adhat hozzá a 3.6-os verzióban végzett ekszerint.

### <a name="compound-indexes-32"></a>Összetett indexek (3.2.)

Az összetett indexek egy dokumentum több mezőjére vonatkozó hivatkozásokat tartalmaznak. Ha összetett indexet szeretne létrehozni, frissítsen a 3.6-os verzióra [támogatási kérelem](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)benyújtásával.

## <a name="next-steps"></a>További lépések

* [Indexelés az Azure Cosmos DB-ben](../cosmos-db/index-policy.md)
* [Adatok lejárata az Azure Cosmos DB-ben automatikusan az élő idővel](../cosmos-db/time-to-live.md)
