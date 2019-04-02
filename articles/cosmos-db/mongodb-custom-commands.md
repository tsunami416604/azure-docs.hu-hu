---
title: Mongodb-hez készült Azure Cosmos DB API-ban tárolt adatok kezelésére szolgáló egyéni parancsok
description: Ez a cikk ismerteti az egyéni parancsok használata kezelheti a mongodb-hez készült Azure Cosmos DB API-ban tárolt adatokkal.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: 238ba2722fef52d4607a7832113c03c097ef90b3
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806890"
---
# <a name="use-custom-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Egyéni parancsok használatával a mongodb-hez az Azure Cosmos DB API-ban tárolt adatok kezelése 

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Kommunikálhat az Azure Cosmos DB API a mongodb-hez a nyílt forráskódú bármelyikével [MongoDB-ügyfélillesztőn](https://docs.mongodb.org/ecosystem/drivers). Az Azure Cosmos DB MongoDB API-ügyfélillesztők meglévő ügyfélillesztők használatát teszi lehetővé a [MongoDB átviteli protokoll](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

API-val az Azure Cosmos DB MongoDB-hez, például a globális terjesztés, automatikus horizontális skálázást, magas rendelkezésre állású, késési garancia, automatikus, titkosítás inaktív állapotban, biztonsági mentés, a Cosmos DB előnyeit élvezheti, és számos további, miközben megőrzi a befektetések a MongoDB-alkalmazás.

## <a name="mongodb-protocol-support"></a>Protokolltámogatás mongodb-hez

Alapértelmezés szerint az Azure Cosmos DB API a MongoDB kompatibilis a MongoDB kiszolgálóverzió 3.2-es, további részletekért lásd: [funkcióihoz és szintaxisához támogatott](mongodb-feature-support.md). Az új funkció a MongoDB 3.4-es verziójú lekérdezési operátorokkal vagy szolgáltatások érhetők el jelenleg előzetes verzióként az Azure Cosmos DB API a mongodb-hez. A következő egyéni parancsok az Azure Cosmos DB bizonyos funkciók támogatásához, ha a mongodb-hez készült Azure Cosmos DB API-ban tárolt adatokkal kapcsolatos CRUD-műveleteket:

* [Adatbázis létrehozása](#create-database)
* [Adatbázis frissítése](#update-database)
* [Adatbázis beolvasása](#get-database)
* [Gyűjtemény létrehozása](#create-collection)
* [Gyűjtemény frissítése](#update-collection)
* [Gyűjtemény beolvasása](#get-collection)

## <a id="create-database"></a> Adatbázis létrehozása

A create database egyéni parancs létrehoz egy új MongoDB-adatbázist. Az adatbázisnév használatos az adatbázisok környezetéből, amelyre vonatkozóan a parancs végrehajtásakor. A Documentclient parancs formátuma a következőképpen:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

A következő táblázat ismerteti a paraméterek belül a parancsot:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| CustomAction   |  sztring  |   Neve az egyéni parancs "CreateDatabase" kell lennie.      |
| offerThroughput | int  | Kiosztott átviteli sebesség, amelyet az adatbázis. Ez a paraméter nem kötelező. |

### <a name="output"></a>Kimenet

Egyéni parancs alapértelmezett választ adja vissza. Tekintse meg a [alapértelmezett kimeneti](#default-output) egyéni parancs kimenetében a paraméterek.

### <a name="examples"></a>Példák

**Adatbázis létrehozása**

Hozzon létre egy "teszt" nevű adatbázist, használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Új adatbázis létrehozása az átviteli sebesség**

Adatbázis létrehozása 1000 kérelemegységet kiosztott átviteli sebesség és a "teszt" nevű, használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> Adatbázis frissítése

A frissítés egyéni adatbázis-parancsot frissíti a megadott adatbázis társított tulajdonságokat. Jelenleg csak akkor frissíthető a "offerThroughput" tulajdonság.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

A következő táblázat ismerteti a paraméterek belül a parancsot:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| CustomAction    |    sztring     |   Az egyéni parancs neve. "UpdateDatabase" kell lennie.      |
|  offerThroughput   |  int       |     Új kiosztott átviteli sebesség, amely beállítja az adatbázison.    |

### <a name="output"></a>Kimenet

Egyéni parancs alapértelmezett választ adja vissza. Tekintse meg a [alapértelmezett kimeneti](#default-output) egyéni parancs kimenetében a paraméterek.

### <a name="examples"></a>Példák

**A kiosztott átviteli sebesség társított adatbázis frissítése**

Frissítse a kiosztott átviteli sebesség az adatbázis neve "test" 1200-as csökkenti, használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> Adatbázis beolvasása

A get-adatbázis egyéni parancs visszaadja az adatbázis-objektum. Az adatbázis neve lesz használva, az az adatbázis-környezet, amelyre vonatkozóan a parancs végrehajtásakor.

```
{
  customAction: "GetDatabase"
}
```

A következő táblázat ismerteti a paraméterek belül a parancsot:


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  CustomAction   |   sztring      |   Az egyéni parancs neve. "GetDatabase" kell lennie.|
        
### <a name="output"></a>Kimenet

Ha a parancs sikeres, a válasz tartalmazza a dokumentum a következő mezőket:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Válasz állapota. 1 == sikeres. 0 sikertelen ==.      |
| `database`    |    `string`        |   Az adatbázis nevét.      |
|   `provisionedThroughput`  |    `int`      |    Kiosztott átviteli sebesség, és állítsa be az adatbázison. Ez az egy válasz nem kötelező paraméter.     |

Ha a parancs sikertelen, alapértelmezett egyéni parancs választ ad vissza. Tekintse meg a [alapértelmezett kimeneti](#default-output) egyéni parancs kimenetében a paraméterek.

### <a name="examples"></a>Példák

**Az adatbázis beolvasása**

A "teszt" nevű adatbázis az adatbázis-objektum lekéréséhez használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> Gyűjtemény létrehozása

Gyűjtemény létrehozása egyéni parancs létrehoz egy új MongoDB-gyűjteményt. Az adatbázisnév használatos az adatbázisok környezetéből, amelyre vonatkozóan a parancs végrehajtásakor. A CreateCollection parancs formátuma a következőképpen:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

A következő táblázat ismerteti a paraméterek belül a parancsot:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| CustomAction    | sztring | Az egyéni parancs neve. "CreateDatabase" kell lennie.     |
| gyűjtemény      | sztring | A gyűjtemény neve                                   |
| offerThroughput | int    | Kiosztott átviteli sebesség beállítása az adatbázison. Ez egy nem kötelező paraméter |
| shardKey        | sztring | Szilánkleképezés-kulcs elérési útja horizontálisan skálázott gyűjtemények. Ez egy nem kötelező paraméter |

### <a name="output"></a>Kimenet

Egyéni parancs alapértelmezett választ adja vissza. Tekintse meg a [alapértelmezett kimeneti](#default-output) egyéni parancs kimenetében a paraméterek.

### <a name="examples"></a>Példák

**Unsharded gyűjtemény létrehozása**

A neve "testCollection" és a kiosztott átviteli sebesség 1000 kérelemegységet unsharded gyűjtemény létrehozásához használja a következő parancsot: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Szilánkokra osztott gyűjtemény létrehozása**

Hozzon létre egy gyűjteményt a neve "testCollection" és a kiosztott átviteli sebesség 1000 kérelemegységet, használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> Gyűjtemény frissítése

Az update-gyűjtemény egyéni parancs a megadott gyűjteményhez társított tulajdonságainak frissítése.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

A következő táblázat ismerteti a paraméterek belül a parancsot:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  CustomAction   |   sztring      |   Az egyéni parancs neve. "UpdateCollection" kell lennie.      |
|  gyűjtemény   |   sztring      |   A gyűjtemény neve.       |
| offerThroughput   |int|   Kiosztott átviteli sebesség beállítása a gyűjteményben.|

## <a name="output"></a>Kimenet

Egyéni parancs alapértelmezett választ adja vissza. Tekintse meg a [alapértelmezett kimeneti](#default-output) egyéni parancs kimenetében a paraméterek.

### <a name="examples"></a>Példák

**A kiosztott átviteli sebesség a gyűjteményhez társított frissítése**

1200-as csökkenti a kiosztott átviteli sebesség a neve "testCollection" gyűjtemény frissítéséhez használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> Gyűjtemény beolvasása

A get-gyűjtemény egyéni parancs visszaadja a gyűjtemény objektumra vonatkozóan.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

A következő táblázat ismerteti a paraméterek belül a parancsot:


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| CustomAction    |   sztring      |   Az egyéni parancs neve. "GetCollection" kell lennie.      |
| gyűjtemény    |    sztring     |    A gyűjtemény neve.     |

### <a name="output"></a>Kimenet

Ha a parancs sikeres, a válasz a következő mezőkkel dokumentumot tartalmaz


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Válasz állapota. 1 == sikeres. 0 sikertelen ==.      |
| `database`    |    `string`     |   Az adatbázis nevét.      |
| `collection`    |    `string`     |    A gyűjtemény neve.     |
|  `shardKeyDefinition`   |   `document`      |  A szegmenskulcs használja az index specifikáció szerinti dokumentum. Ez az egy válasz nem kötelező paraméter.       |
|  `provisionedThroughput`   |   `int`      |    Kiosztott átviteli sebesség beállítása a gyűjteményben. Ez az egy válasz nem kötelező paraméter.     |

Ha a parancs sikertelen, alapértelmezett egyéni parancs választ ad vissza. Tekintse meg a [alapértelmezett kimeneti](#default-output) egyéni parancs kimenetében a paraméterek.

### <a name="examples"></a>Példák

**A gyűjtemény beolvasása**

Az objektum "testCollection" nevű gyűjtemény lekéréséhez használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> Egy egyéni parancs kimenete alapértelmezés

Ha nincs megadva, az egyéni válasz egy dokumentumot, a következő mezőket tartalmazza:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Válasz állapota. 1 == sikeres. 0 sikertelen ==.      |
| `code`    |   `int`      |   Csak adja vissza, ha a parancs végrehajtása sikertelen volt (azaz ok == 0). A MongoDB-hibakódot tartalmazza. Ez az egy válasz nem kötelező paraméter.      |
|  `errMsg`   |  `string`      |    Csak adja vissza, ha a parancs végrehajtása sikertelen volt (azaz ok == 0). Egy felhasználóbarát hibaüzenetet tartalmaz. Ez az egy válasz nem kötelező paraméter.      |

## <a name="next-steps"></a>További lépések

Ismerje meg a következő Azure Cosmos DB-fogalmak folytassa tovább: 

* [Az Azure Cosmos DB indexelése](../cosmos-db/index-policy.md)
* [Adatok az Azure Cosmos DB automatikusan az élettartam elévülése](../cosmos-db/time-to-live.md)