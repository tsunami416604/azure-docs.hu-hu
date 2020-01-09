---
title: Az MongoDB Azure Cosmos DB API-MongoDB való kezeléséhez szükséges bővítmény-parancsok
description: Ez a cikk azt ismerteti, hogyan használhatók a MongoDB-bővítmény parancsai a Azure Cosmos DB API-MongoDB tárolt adatkezelési szolgáltatásokhoz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445221"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-bővítményi parancsok használata a Azure Cosmos DB API-MongoDB tárolt adatkezeléshez 

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. A MongoDB Azure Cosmos DB API-jával a nyílt forráskódú [MongoDB-ügyfelek](https://docs.mongodb.org/ecosystem/drivers)bármelyikének használatával kommunikálhat. A MongoDB Azure Cosmos DB API-je lehetővé teszi a meglévő ügyféloldali illesztőprogramok használatát az [MongoDB Wire protokoll](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)betartásával.

A MongoDB-hez készült Azure Cosmos DB API-val élvezheti a globális elosztást, az automatikus horizontális skálázást, a magas rendelkezésre állást, Cosmos DB a késési garanciákat, az automatikus titkosítást, a nyugalmi állapotot, a biztonsági mentéseket és sok más más, a beruházások megőrzése mellett. a MongoDB alkalmazásban.

## <a name="mongodb-protocol-support"></a>MongoDB protokoll támogatása

Alapértelmezés szerint a MongoDB Azure Cosmos DB API-je kompatibilis a MongoDB Server 3,2-es verziójával, további részletekért lásd a [támogatott szolgáltatások és szintaxis](mongodb-feature-support.md)című témakört. A MongoDB 3,4-es verziójában hozzáadott funkciók vagy lekérdezési operátorok jelenleg előzetes verzióként érhetők el a Azure Cosmos DB API-MongoDB. A következő bővítmény-parancsok a Azure Cosmos DB API-MongoDB tárolt adatokon végzett szifilisz-műveletek végrehajtásakor Azure Cosmos DB speciális funkciókat támogatják:

* [Adatbázis létrehozása](#create-database)
* [Adatbázis frissítése](#update-database)
* [Adatbázis beolvasása](#get-database)
* [Gyűjtemény létrehozása](#create-collection)
* [Gyűjtemény frissítése](#update-collection)
* [Gyűjtemény beolvasása](#get-collection)

## <a id="create-database"></a>Adatbázis létrehozása

Az adatbázis-bővítmény létrehozása parancs létrehoz egy új MongoDB-adatbázist. Az adatbázis neve azon adatbázisok környezetében használatos, amelyeken a parancs végrehajtása történik. A CreateDatabase parancs formátuma a következő:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| customAction   |  sztring  |   Az egyéni parancs neve csak "CreateDatabase" lehet.      |
| offerThroughput | int  | Az adatbázison beállított kiépített átviteli sebesség. Ezt a paramétert nem kötelező megadni. |

### <a name="output"></a>Kimenet

Egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

**Adatbázis létrehozása**

A "test" nevű adatbázis létrehozásához használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Adatátviteli sebességű adatbázis létrehozása**

A "test" nevű és a 1000 RUs kiépített átviteli sebességű adatbázis létrehozásához használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a>Adatbázis frissítése

Az adatbázis-kiterjesztés frissítése parancs frissíti a megadott adatbázishoz társított tulajdonságokat. Jelenleg csak a "offerThroughput" tulajdonságot lehet frissíteni.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| customAction    |    sztring     |   Az egyéni parancs neve. A következőnek kell lennie: "UpdateDatabase".      |
|  offerThroughput   |  int       |     Az adatbázison beállítani kívánt új, kiépített átviteli sebesség.    |

### <a name="output"></a>Kimenet

Egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

**Adatbázishoz társított kiépített átviteli sebesség frissítése**

A "teszt 1200" nevű adatbázis kiépített átviteli sebességének frissítéséhez használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a>Adatbázis beolvasása

Az adatbázis-kiterjesztés beolvasása parancs visszaadja az adatbázis-objektumot. Az adatbázis neve a parancs végrehajtásához használt adatbázis-környezetből származik.

```
{
  customAction: "GetDatabase"
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  customAction   |   sztring      |   Az egyéni parancs neve. "GetDatabase" értéknek kell lennie|
        
### <a name="output"></a>Kimenet

Ha a parancs sikeres, a válasz a következő mezőket tartalmazó dokumentumot tartalmazza:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |   `int`     |   A válasz állapota. 1 = = sikeres. 0 = = hiba.      |
| `database`    |    `string`        |   Az adatbázis neve.      |
|   `provisionedThroughput`  |    `int`      |    Az adatbázison beállított kiépített átviteli sebesség. Ez egy opcionális válasz paraméter.     |

Ha a parancs végrehajtása sikertelen, a rendszer egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

**Adatbázis beolvasása**

A "test" nevű adatbázis adatbázis-objektumának beszerzéséhez használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a>Gyűjtemény létrehozása

A gyűjtemény létrehozása bővítmény parancs egy új MongoDB-gyűjteményt hoz létre. Az adatbázis neve azon adatbázisok környezetében használatos, amelyeken a parancs végrehajtása történik. A CreateCollection parancs formátuma a következő:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| customAction    | sztring | Az egyéni parancs neve. "CreateCollection" értéknek kell lennie     |
| gyűjtemény      | sztring | A gyűjtemény neve                                   |
| offerThroughput | int    | Az adatbázison beállított kiépített átviteli sebesség. Ez egy opcionális paraméter |
| shardKey        | sztring | A szegmens kulcs elérési útja a szilánkokra bontott gyűjtemény létrehozásához. Ez egy opcionális paraméter |

### <a name="output"></a>Kimenet

Egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

**Nem szilánkos gyűjtemény létrehozása**

A "testCollection" névvel és a 1000 RUs kiépített átviteli sebességgel rendelkező, nem szétosztott gyűjtemény létrehozásához használja a következő parancsot: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Szilánkokra osztott gyűjtemény létrehozása**

Az alábbi paranccsal hozhat létre egy "testCollection" nevű és 1000 RUs kiépített adatátviteli sebességű, többrészes gyűjteményt:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a>Gyűjtemény frissítése

Az Update Collection Extension parancs frissíti a megadott gyűjteményhez társított tulajdonságokat.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  customAction   |   sztring      |   Az egyéni parancs neve. A következőnek kell lennie: "UpdateCollection".      |
|  gyűjtemény   |   sztring      |   A gyűjtemény neve.       |
| offerThroughput   |int|   Kiosztott átviteli sebesség a gyűjteményre való beállításhoz.|

## <a name="output"></a>Kimenet

Egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

**Egy gyűjteményhez társított kiosztott teljesítmény frissítése**

A "testCollection 1200" nevű gyűjtemény kiépített átviteli sebességének frissítéséhez használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a>Gyűjtemény beolvasása

A gyűjtemény egyéni beolvasása parancs a gyűjtemény objektumot adja vissza.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| customAction    |   sztring      |   Az egyéni parancs neve. A következőnek kell lennie: "GetCollection".      |
| gyűjtemény    |    sztring     |    A gyűjtemény neve.     |

### <a name="output"></a>Kimenet

Ha a parancs sikeres, a válasz a következő mezőket tartalmazó dokumentumot tartalmazza


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |    `int`     |   A válasz állapota. 1 = = sikeres. 0 = = hiba.      |
| `database`    |    `string`     |   Az adatbázis neve.      |
| `collection`    |    `string`     |    A gyűjtemény neve.     |
|  `shardKeyDefinition`   |   `document`      |  A kiosztott kulcsként használt index-specifikációs dokumentum. Ez egy opcionális válasz paraméter.       |
|  `provisionedThroughput`   |   `int`      |    Kiosztott átviteli sebesség a gyűjteményre való beállításhoz. Ez egy opcionális válasz paraméter.     |

Ha a parancs végrehajtása sikertelen, a rendszer egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

**A gyűjtemény beolvasása**

A "testCollection" nevű gyűjteményhez tartozó gyűjtemény objektum beszerzéséhez használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a>Egyéni parancs alapértelmezett kimenete

Ha nincs megadva, az egyéni válasz a következő mezőket tartalmazó dokumentumot tartalmazza:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |    `int`     |   A válasz állapota. 1 = = sikeres. 0 = = hiba.      |
| `code`    |   `int`      |   Csak akkor tér vissza, ha a parancs végrehajtása sikertelen volt (például ok = = 0). A MongoDB hibakódot tartalmazza. Ez egy opcionális válasz paraméter.      |
|  `errMsg`   |  `string`      |    Csak akkor tér vissza, ha a parancs végrehajtása sikertelen volt (például ok = = 0). Felhasználóbarát hibaüzenetet tartalmaz. Ez egy opcionális válasz paraméter.      |

## <a name="next-steps"></a>Következő lépések

Ezután folytassa a következő Azure Cosmos DB fogalmak megismerésével: 

* [Indexelés Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Az Azure Cosmos DB automatikusan lejár az idő az élettartammal](../cosmos-db/time-to-live.md)
