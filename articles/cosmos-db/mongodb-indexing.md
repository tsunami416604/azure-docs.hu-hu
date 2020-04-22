---
title: Indexelés kezelése az Azure Cosmos DB MongoDB-hoz elérhető API-jában
description: Ez a cikk áttekintést nyújt az Azure Cosmos DB indexelési képességeiről a MongoDB API használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732706"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Indexelés kezelése az Azure Cosmos DB MongoDB-hoz elérhető API-jában

Az Azure Cosmos DB MongoDB-hoz való API-ja kihasználja az Azure Cosmos DB alapvető indexkezelési képességeit. Ez a cikk az Azure Cosmos DB MongoDB API-jával történő indexek hozzáadására összpontosít. Az [Azure Cosmos DB indexelésének áttekintését](index-overview.md) is elolvashatja, amely minden API-ban releváns.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexelés a MongoDB kiszolgáló 3.6-os verziójához

Az Azure Cosmos DB API-ja a MongoDB-kiszolgáló `_id` 3.6-os verziójához automatikusan indexeli a mezőt, amely et nem lehet eldobni. Automatikusan kényszeríti a `_id` mező egyediségét shard kulcsonként.

További mezők indexeléséhez alkalmazza a MongoDB indexkezelő parancsokat. A MongoDB-hoz is csak az Azure Cosmos DB MongoDB-alapú API-ja csak a `_id` mezőt indexeli. Ez az alapértelmezett indexelési szabályzat eltér az Azure Cosmos DB SQL API-tól, amely alapértelmezés szerint az összes mezőt indexeli.

Ha rendezést szeretne alkalmazni egy lekérdezésre, létre kell hoznia egy indexet a rendezési műveletben használt mezőkön.

## <a name="index-types"></a>Indextípusok

### <a name="single-field"></a>Egyetlen mező

Indexeket bármely mezőben létrehozhat. Az egymezős index rendezési sorrendje nem számít. A következő parancs indexet `name`hoz létre a mezőben:

`db.coll.createIndex({name:1})`

Egy lekérdezés több egymezős indexet használ, ahol elérhető. Tárolónként legfeljebb 500 egymezős indexet hozhat létre.

### <a name="compound-indexes-mongodb-server-version-36"></a>Összetett indexek (MongoDB szerver 3.6-os verziója)

Az Azure Cosmos DB MongoDB API-ja támogatja a 3.6-os verziójú vezetékes protokollt használó fiókok összetett indexeit. Összetett indexbe legfeljebb nyolc mezőt vehet fel. A MongoDB-val ellentétben csak akkor hozzon létre összetett indexet, ha a lekérdezésnek egyszerre több mezőben kell hatékonyan rendeznie. A több szűrővel rendelkező lekérdezések esetében, amelyeket nem kell rendezni, hozzon létre több egymezős indexet egyetlen összetett index helyett.

A következő parancs összetett indexet `name` `age`hoz létre a mezőkön és a következőterületeken:

`db.coll.createIndex({name:1,age:1})`

Az összetett indexek segítségével egyszerre több mezőt is hatékonyan rendezhet, ahogy az a következő példában látható:

`db.coll.find().sort({name:1,age:1})`

Az előző összetett index segítségével hatékonyan rendezheti az ellenkező rendezési sorrendű lekérdezéseket az összes mezőben. Például:

`db.coll.find().sort({name:-1,age:-1})`

Az összetett indexben lévő görbék sorrendjének azonban pontosan meg kell egyeznie a lekérdezéssel. Íme egy példa egy olyan lekérdezésre, amely további összetett indexet igényel:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Többkulcsos indexek

Az Azure Cosmos DB többkulcsos indexeket hoz létre a tömbökben tárolt tartalom indexeléséhez. Ha egy tömbértékkel rendelkező mezőt indexel, az Azure Cosmos DB automatikusan indexeli a tömb minden elemét.

### <a name="geospatial-indexes"></a>Térinformatikai indexek

Számos térinformatikai operátor számára előnyösek lesznek a térinformatikai indexek. Jelenleg az Azure Cosmos DB MongoDB `2dsphere` API-ja támogatja az indexeket. Az API még `2d` nem támogatja az indexeket.

Íme egy példa a térinformatikai index `location` létrehozására a mezőben:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Szövegindexek

Az Azure Cosmos DB MongoDB API-ja jelenleg nem támogatja a szövegindexeket. A karakterláncok szöveges keresési lekérdezések, az [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) integráció az Azure Cosmos DB használatával.

## <a name="index-properties"></a>Index tulajdonságai

A következő műveletek gyakoriak a 3.6-os vezetékes protokollverziót kiszolgáló fiókok és a korábbi verziókat kiszolgáló fiókok esetében. A [támogatott indexekről és az indexelt tulajdonságokról](mongodb-feature-support-36.md#indexes-and-index-properties)további információért kaphat többet.

### <a name="unique-indexes"></a>Egyedi indexek

[Az egyedi indexek](unique-keys.md) akkor hasznosak, ha azt a kontróba kényszeríti, hogy két vagy több dokumentum nem tartalmazza ugyanazt az értéket az indexelt mezőknél.

> [!IMPORTANT]
> Egyedi indexek csak akkor hozhatók létre, ha a gyűjtemény üres (nem tartalmaz dokumentumokat).

A következő parancs egyedi indexet `student_id`hoz létre a mezőben:

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

Szilánkos gyűjtemények, meg kell adnia a szegmens (partíció) kulcs egy egyedi index létrehozásához. Ez azt jelenti, hogy a megosztott gyűjteményekben esetében az összes egyedi index összetett index, és az egyik mező a partíciókulcs.

A következő parancsok létrehoznak ```coll``` egy szilánkos gyűjteményt (a ```university```szegmenskulcs) `student_id` `university`egyedi indexszel a mezőkön és:

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

Az előző példában a ```"university":1``` záradék kihagyásával a következő üzenet teljében jelenik meg:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL-indexek

Ahhoz, hogy egy adott gyűjteményben engedélyezze a dokumentumok lejáratát, létre kell hoznia egy [létrehozási idő (TTL) indexet.](../cosmos-db/time-to-live.md) A TTL index a `_ts` mező ben `expireAfterSeconds` lévő index, amelynek értéke van.

Példa:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Az előző parancs törli a ```db.coll``` gyűjteményben lévő azon dokumentumokat, amelyeket az elmúlt 10 másodpercben nem módosítottak.

> [!NOTE]
> A **_ts** mező az Azure Cosmos DB-re jellemző, és nem érhető el a MongoDB-ügyfelektől. Ez egy fenntartott (rendszer) tulajdonság, amely a dokumentum utolsó módosításának időbélyegzőjét tartalmazza.

## <a name="track-index-progress"></a>Indexállapotának nyomon követése

Az Azure Cosmos DB MongoDB-hoz való API-jának 3.6-os verziója támogatja az `currentOp()` index előrehaladásának nyomon követését egy adatbázis-példányon. Ez a parancs olyan dokumentumot ad vissza, amely egy adatbázis-példány folyamatban lévő műveleteivel kapcsolatos információkat tartalmaz. A paranccsal nyomon követheti az `currentOp` összes folyamatban lévő műveletet a natív MongoDB-ban. Az Azure Cosmos DB MongoDB API-jában ez a parancs csak az indexművelet nyomon követését támogatja.

Íme néhány példa arra, hogy `currentOp` miként használhatja a parancsot az indexállapotának nyomon követésére:

* Gyűjtemény indexének előrehaladása:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Az adatbázis összes gyűjteményének indexállapotának begyűjtése:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Az Azure Cosmos-fiók összes adatbázisának és gyűjteményének indexfolyamatának begyűjtése:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Példák az index előrehaladási kimenetére

Az index előrehaladási részletei az aktuális indexművelet előrehaladásának százalékos arányát mutatják. Íme egy példa, amely az index előrehaladásának különböző szakaszainak kimeneti dokumentumformátumát mutatja be:

- Egy "foo" gyűjtemény és a "sáv" adatbázis 60 százalékos készültségi mutatóján végzett indexművelet a következő kimeneti dokumentummal fog rendelkezni. A `Inprog[0].progress.total` mezőben 100 látható a célteljesítési százalék.

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

- Ha egy indexművelet csak most kezdődött el egy "foo" gyűjtemény és a "bar" adatbázis, a kimeneti dokumentum 0 százalékos előrehaladást mutat, amíg el nem éri a mérhető szintet.

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

- Amikor a folyamatban lévő index művelet befejeződik, `inprog` a kimeneti dokumentum üres műveleteket jelenít meg.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Háttérindex frissítései

A **Háttérindex** tulajdonsághoz megadott értéktől függetlenül az indexfrissítések mindig a háttérben történik. Mivel az indexfrissítések a kérelemegységeket (RT)-t a többi adatbázis-műveletnél alacsonyabb prioritással használják fel, az indexmódosítások nem eredményeznek állásidőt az írások, frissítések vagy törlések esetében.

Új index hozzáadásakor a lekérdezések azonnal használni fogják az indexet. Ez azt jelenti, hogy előfordulhat, hogy a lekérdezések nem adják vissza az összes egyező eredményt, és ezt a hibák visszaadása nélkül. Amikor az index átalakítás befejeződik, a lekérdezés eredményei konzisztensek lesznek. Nyomon [követheti az index előrehaladását.](#track-index-progress)

## <a name="migrate-collections-with-indexes"></a>Gyűjtemények áttelepítése indexekkel

Jelenleg csak akkor hozhat létre egyedi indexeket, ha a gyűjtemény nem tartalmaz dokumentumokat. A népszerű MongoDB áttelepítési eszközök az adatok importálása után próbálják meg létrehozni az egyedi indexeket. A probléma megkerüléséhez manuálisan is létrehozhatja a megfelelő gyűjteményeket és egyedi indexeket ahelyett, hogy engedélyezené az áttelepítési eszköz kipróbálását. (Ezt a ```mongorestore``` viselkedést a `--noIndexRestore` parancssorjelző használatával érheti el.)

## <a name="indexing-for-mongodb-version-32"></a>Indexelés a MongoDB 3.2-es verziójához

A rendelkezésre álló indexelési funkciók és alapértelmezett értékek eltérnek a MongoDB vezetékes protokoll 3.2-es verziójával kompatibilis Azure Cosmos-fiókokesetében. [Ellenőrizheti a fiók verzióját.](mongodb-feature-support-36.md#protocol-support) A 3.6-os verzióra támogatási kérelem benyújtásával [frissíthet.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

A 3.2-es verzió használata esetén ez a szakasz a 3.6-os verzióval való főbb különbségeket ismerteti.

### <a name="dropping-default-indexes-version-32"></a>Alapértelmezett indexek elvetése (3.2-es verzió)

Az Azure Cosmos DB MongoDB-hoz való API-jának 3.6-os verziójával ellentétben a 3.2-es verzió alapértelmezés szerint minden tulajdonságot indexel. A következő paranccsal eldobhatja ezeket az```coll```alapértelmezett indexeket egy gyűjteményhez ( ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Az alapértelmezett indexek elvetése után további indexeket adhat hozzá, mint a 3.6-os verzióban.

### <a name="compound-indexes-version-32"></a>Összetett indexek (3.2-es verzió)

Az összetett indexek egy dokumentum több mezőjére vonatkozó hivatkozásokat tartalmaznak. Ha összetett indexet szeretne létrehozni, frissítsen a 3.6-os verzióra [támogatási kérelem](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)benyújtásával.

## <a name="next-steps"></a>További lépések

* [Indexelés az Azure Cosmos DB-ben](../cosmos-db/index-policy.md)
* [Adatok lejárata az Azure Cosmos DB-ben automatikusan az élő idővel](../cosmos-db/time-to-live.md)
