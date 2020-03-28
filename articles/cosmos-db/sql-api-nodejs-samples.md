---
title: Node.js példák az Azure Cosmos-adatbázis adatainak kezeléséhez
description: A GitHub tartalmaz az Azure Cosmos DB általános feladataihoz, többek között a CRUD-műveletekhez kötődő Node.js-példákat.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.openlocfilehash: 4ac3d74cb76a936cc88261a2fedf16764e8ae52f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76718293"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Node.js példák az Azure Cosmos DB-ben lévő adatok kezeléséhez

> [!div class="op_single_selector"]
> * [.](sql-api-dotnet-samples.md)
> * [.](sql-api-dotnet-v3sdk-samples.md)
> * [Java-példák](sql-api-java-samples.md)
> * [Aszinkron Java-példák](sql-api-async-java-samples.md)
> * [Példák a Node.js](sql-api-nodejs-samples.md)
> * [Példák python](sql-api-python-samples.md)
> * [Azure Kódminta-katalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Az [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) GitHub-adattárban találhatók olyan mintamegoldások, amelyek CRUD- és egyéb általános műveleteket végeznek Azure Cosmos DB-erőforrásokon. Ez a cikk a következő információkat tartalmazza:

* Az egyes Node.js-példák projektfájljaiban a feladatokra mutató hivatkozások.
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

A [JavaScript SDK](sql-api-sdk-node.md)-ra is szüksége lesz.
   
   > [!NOTE]
   > Minden minta önálló, magát állítja be, és végül kitakarít maga után. Így a minták egyszerre több hívást intéznek a [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) felé. Minden ilyen hívás esetén az előfizetésére 1 órányi használat terhelődik a létrehozott tároló teljesítményszintjének megfelelően.
   > 
   > 

## <a name="database-examples"></a>Adatbázis-példák

A [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) fájl bemutatja, hogyan hajthatja végre a CRUD-műveleteket az adatbázisban. Az Azure Cosmos-adatbázisok futtatása előtt a következő mintákfuttatásáról az Adatbázisok, tárolók és elemek fogalmi [cikkének munkáért.](databases-containers-items.md) 

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása, ha még nem létezik](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#createifnotexists-databaserequest--requestoptions-) |
| [Fiók adatbázisainak felsorolása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#readall-feedoptions-) |
| [Adatbázis olvasása azonosító szerint](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest#read-requestoptions-) |
| [Adatbázis törlése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest#delete-requestoptions-) |

## <a name="container-examples"></a>Példák tárolókra

A [ContainerManagement-fájl](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) bemutatja, hogyan hajthatja végre a CRUD-műveleteket a tárolón. Az Azure Cosmos-gyűjtemények futtatása előtt a következő mintákat, az [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikk kidolgozása című témakörben olvashat. 

| Tevékenység | API-referencia |
| --- | --- |
| [Tároló létrehozása, ha még nem létezik](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#createifnotexists-containerrequest--requestoptions-) |
| [Fiók tárolóinak felsorolása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#readall-feedoptions-) |
| [Tárolódefiníció olvasása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest#read-requestoptions-) |
| [Tároló törlése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest#delete-requestoptions-) |

## <a name="item-examples"></a>Példák elemekre

Az [ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) fájl bemutatja, hogyan hajthatja végre a CRUD-műveleteket az elemen. Az Azure Cosmos-dokumentumok futtatása előtt a következő mintákfuttatásáról az Adatbázisok, tárolók és elemek fogalmi [cikkének munkáért.](databases-containers-items.md) 

| Tevékenység | API-referencia |
| --- | --- |
| [Elemek létrehozása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest#create-t--requestoptions-) |
| [A tároló összes elemének beolvasása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest#readall-feedoptions-) |
| [Elem beolvasása azonosító alapján](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-) |
| [Az elem olvasása csak akkor, ha az elem megváltozott](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Dokumentumok lekérdezése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Elem cseréje](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Elem cseréje feltételes ETag-ellenőrzéssel](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Elem törlése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Indexelési példák

Az [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) fájl bemutatja, hogyan kezelhető az indexelés. Ha többet szeretne tudni az Indexelés az Azure Cosmos DB futtatása előtt a következő mintákat, olvassa el az [indexelési szabályzatok](index-policy.md), [indexelési típusok](index-types.md)és [indexelési útvonalak fogalmi](index-paths.md) cikkek. 

| Tevékenység | API-referencia |
| --- | --- |
| [Adott elem manuális indexelése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Adott elem manuális kizárása az indexből](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Elérési út kizárása az indexből](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Tartományindex létrehozása sztringútvonalon](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Tároló létrehozása alapértelmezett indexPolicyvel, majd ennek online frissítése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Kiszolgálóoldali programozási példák

A [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) projekt [index.ts](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) fájlja bemutatja, hogyan hajthatja végre a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos DB kiszolgálóoldali programozásáról, olvassa el a [Tárolt eljárások, eseményindítók és a felhasználó által definiált függvények](stored-procedures-triggers-udfs.md) fogalmi cikkét. 

| Tevékenység | API-referencia |
| --- | --- |
| [Tárolt eljárás létrehozása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Tárolt eljárás végrehajtása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

A kiszolgálóoldali programozásról további információt az [Azure Cosmos DB kiszolgálóoldali programozás ismertetőjének tárolt eljárásokat, adatbázis-triggereket és UDF-eket leíró részében talál](stored-procedures-triggers-udfs.md).

