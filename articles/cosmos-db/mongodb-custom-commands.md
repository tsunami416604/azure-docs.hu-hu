---
title: A MongoDB-bővítmény parancsok az Azure Cosmos DB MongoDB-hoz való API-jában történő kezeléséhez
description: Ez a cikk ismerteti, hogyan mongoDB bővítmény parancsok at az Azure Cosmos DB API-m on MongoDB tárolt adatok kezelésére.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583587"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>A MongoDB-bővítmény parancsaival kezelheti az Azure Cosmos DB MongoDB-hoz való API-jában tárolt adatokat 

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az Azure Cosmos DB MongoDB-hoz való API-jával kommunikálhat a nyílt forráskódú [MongoDB ügyfél-illesztőprogramok](https://docs.mongodb.org/ecosystem/drivers)bármelyikével. Az Azure Cosmos DB MongoDB-hoz való API-ja lehetővé teszi a meglévő ügyfél-illesztőprogramok használatát a [MongoDB vezetékes protokoll](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)betakarásával.

Az Azure Cosmos DB MongoDB API-jának használatával élvezheti a Cosmos DB előnyeit, például a globális disztribúciót, az automatikus horizontális horizontális felszolgálást, a magas rendelkezésre állást, a késésgaranciát, az automatikus, az inaktív titkosítást, a biztonsági mentéseket és még sok mást, miközben megőrzi a MongoDB-alkalmazásban lévő befektetéseit.

## <a name="mongodb-protocol-support"></a>A MongoDB protokoll támogatása

Alapértelmezés szerint az Azure Cosmos DB MongoDB-hoz való API-ja kompatibilis a MongoDB 3.2-es verziójával, további részletekért olvassa el a [támogatott funkciókat és a szintaxist.](mongodb-feature-support.md) A MongoDB 3.4-es verziójában hozzáadott funkciók vagy lekérdezés-operátorok jelenleg előzetes verzióként érhetők el az Azure Cosmos DB MongoDB API-jában. A következő bővítményparancsok támogatják az Azure Cosmos DB adott funkcióit, amikor CRUD-műveleteket hajtanak végre az Azure Cosmos DB MongoDB-hoz készült API-jában tárolt adatokon:

* [Adatbázis létrehozása](#create-database)
* [Adatbázis frissítése](#update-database)
* [Adatbázis bekéselése](#get-database)
* [Gyűjtemény létrehozása](#create-collection)
* [Frissítési gyűjtemény](#update-collection)
* [Gyűjtemény begyűjtése](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Adatbázis létrehozása

Az adatbázis-kiterjesztés létrehozása parancs új MongoDB adatbázist hoz létre. Az adatbázis neve az adatbázis-környezetből származik, amelyen a parancsot végrehajtják. Az Adatbázis létrehozása parancs formátuma a következő:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

Az alábbi táblázat a parancs paramétereit ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| customAction   |  sztring  |   Az egyéni parancs neve, "CreateDatabase" legyen.      |
| ajánlatThroughput | int  | Az adatbázisban beállított kiépített átviteli. Ezt a paramétert nem kötelező megadni. |

### <a name="output"></a>Kimenet

Alapértelmezett egyéni parancsválaszt ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimeneti paraméterekhez.

### <a name="examples"></a>Példák

**Adatbázis létrehozása**

"Teszt" nevű adatbázis létrehozásához használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Adatbázis létrehozása átviteli hatrással**

A "teszt" nevű és 1000 főkiszolgálókra vonatkozó kiépített átviteli hang létrehozásához használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Adatbázis frissítése

A frissítési adatbázis-kiterjesztés parancs frissíti a megadott adatbázishoz tartozó tulajdonságokat. Jelenleg csak az "offerThroughput" tulajdonságot frissítheti.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

Az alábbi táblázat a parancs paramétereit ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| customAction    |    sztring     |   Az egyéni parancs neve. Az "UpdateDatabase" rendszernek kell lennie.      |
|  ajánlatThroughput   |  int       |     Az adatbázisban beállítani kívánt új kiépített átviteli.    |

### <a name="output"></a>Kimenet

Alapértelmezett egyéni parancsválaszt ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimeneti paraméterekhez.

### <a name="examples"></a>Példák

**Az adatbázishoz társított kiosztott átviteli rendszer frissítése**

A "teszt" nevű adatbázis kiosztott átviteli átaputáinak 1200 darab kre-re való frissítéséhez használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Adatbázis bekéselése

Az adatbáziskiterjesztés bekésése parancs az adatbázis-objektumot adja vissza. Az adatbázis neve abból az adatbázis-környezetből származik, amelyen a parancs végrehajtásra kerül.

```
{
  customAction: "GetDatabase"
}
```

Az alábbi táblázat a parancs paramétereit ismerteti:


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  customAction   |   sztring      |   Az egyéni parancs neve. A "GetDatabase" legyen az ."|
        
### <a name="output"></a>Kimenet

Ha a parancs sikeres, a válasz a következő mezőket tartalmazó dokumentumot tartalmazza:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |   `int`     |   A válasz állapota. 1 == siker. 0 == hiba.      |
| `database`    |    `string`        |   Az adatbázis neve.      |
|   `provisionedThroughput`  |    `int`      |    Az adatbázisban beállított kiépített átviteli. Ez egy választható válaszparaméter.     |

Ha a parancs sikertelen, a rendszer egy alapértelmezett egyéni parancsválaszt ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimeneti paraméterekhez.

### <a name="examples"></a>Példák

**Az adatbázis beszereznie**

A "teszt" nevű adatbázis adatbázis-objektumának lekérnie a következő parancsot:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Gyűjtemény létrehozása

A gyűjteménybővítmény létrehozása parancs létrehoz egy új MongoDB-gyűjteményt. Az adatbázis neve az adatbázis-környezetből származik, amelyen a parancsot végrehajtják. A CreateCollection parancs formátuma a következő:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

Az alábbi táblázat a parancs paramétereit ismerteti:

| **Mező** | **Típus** | **Szükséges** | **Leírás** |
|---------|---------|---------|---------|
| customAction | sztring | Kötelező | Az egyéni parancs neve. A "CreateCollection" (CreateCollection) (CreateCollection) (CreateCollection) (CreateCollection) (Create|
| Gyűjtemény | sztring | Kötelező | A gyűjtemény neve. Különleges karakterek nem engedélyezettek.|
| ajánlatThroughput | int | Nem kötelező* | Az adatbázisra beállított kiépített átviteli. Ha ez a paraméter nincs megadva, akkor az alapértelmezett érték a minimum 400 RU/s lesz. * A 10 000 RU/s-on `shardKey` túli átviteli átvitel megadásához a paraméter szükséges.|
| shardkey | sztring | Nem kötelező* | A szilánkos gyűjtemény shard kulcselérési útja. Erre a paraméterre akkor van szükség, ha több mint `offerThroughput`10 000 RU/s-ot állít be a alkalmazásban.  Ha meg van adva, akkor minden beszúrt dokumentumhoz szükség lesz erre az értékre. |

### <a name="output"></a>Kimenet

Alapértelmezett egyéni parancsválaszt ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimeneti paraméterekhez.

### <a name="examples"></a>Példák

**Hardtalan gyűjtemény létrehozása**

A "testCollection" nevű és 1000 főtartalékos kiépített átviteli hatos ú sharded gyűjtemény létrehozásához használja a következő parancsot: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Szilánkos gyűjtemény létrehozása**

A "testCollection" nevű szilánkos gyűjtemény és az 1000 több mint egy főtartalék, valamint az "a.b" shardkey tulajdonság létrehozásához használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Frissítési gyűjtemény

A frissítési gyűjteménybővítmény parancs frissíti a megadott gyűjteményhez társított tulajdonságokat.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

Az alábbi táblázat a parancs paramétereit ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  customAction   |   sztring      |   Az egyéni parancs neve. Az "UpdateCollection" rendszernek kell lennie.      |
|  Gyűjtemény   |   sztring      |   A gyűjtemény neve.       |
| ajánlatThroughput   |int|   A gyűjteményhez beállított átviteli átbocsátás.|

## <a name="output"></a>Kimenet

Alapértelmezett egyéni parancsválaszt ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimeneti paraméterekhez.

### <a name="examples"></a>Példák

**A gyűjteményhez társított kiosztott átviteli-erról**

A "testCollection" nevű gyűjtemény kiosztott átviteli takarásának 1200 több mint kódra történő frissítéséhez használja a következő parancsot:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Gyűjtemény begyűjtése

A get collection egyéni parancs a gyűjteményobjektumot adja vissza.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

Az alábbi táblázat a parancs paramétereit ismerteti:


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| customAction    |   sztring      |   Az egyéni parancs neve. A "GetCollection" lesz.      |
| Gyűjtemény    |    sztring     |    A gyűjtemény neve.     |

### <a name="output"></a>Kimenet

Ha a parancs sikeres, a válasz a következő mezőket tartalmazó dokumentumot tartalmaz


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |    `int`     |   A válasz állapota. 1 == siker. 0 == hiba.      |
| `database`    |    `string`     |   Az adatbázis neve.      |
| `collection`    |    `string`     |    A gyűjtemény neve.     |
|  `shardKeyDefinition`   |   `document`      |  A szegmenskulcsként használt indexspecifikációs dokumentum. Ez egy választható válaszparaméter.       |
|  `provisionedThroughput`   |   `int`      |    Kiépített átviteli a gyűjtemény beállítása. Ez egy választható válaszparaméter.     |

Ha a parancs sikertelen, a rendszer egy alapértelmezett egyéni parancsválaszt ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimeneti paraméterekhez.

### <a name="examples"></a>Példák

**Szerezd meg a gyűjteményt**

A "testCollection" nevű gyűjtemény gyűjteményének gyűjteményének lekérnie a következő parancsot:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Egyéni parancs alapértelmezett kimenete

Ha nincs megadva, az egyéni válasz a következő mezőket tartalmazó dokumentumot tartalmazza:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |    `int`     |   A válasz állapota. 1 == siker. 0 == hiba.      |
| `code`    |   `int`      |   Csak akkor adott vissza, ha a parancs nem sikerült (azaz ok == 0). A MongoDB hibakódot tartalmazza. Ez egy választható válaszparaméter.      |
|  `errMsg`   |  `string`      |    Csak akkor adott vissza, ha a parancs nem sikerült (azaz ok == 0). Felhasználóbarát hibaüzenetet tartalmaz. Ez egy választható válaszparaméter.      |

## <a name="next-steps"></a>További lépések

Ezután megismerheti az Alábbi Azure Cosmos DB-fogalmakat: 

* [Indexelés az Azure Cosmos DB-ben](../cosmos-db/index-policy.md)
* [Adatok lejárata az Azure Cosmos DB-ben automatikusan az élő idővel](../cosmos-db/time-to-live.md)
