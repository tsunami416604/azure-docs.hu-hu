---
title: 'Az Azure Cosmos DB: Tömeges végrehajtó .NET API, SDK &-erőforrások'
description: Ismerkedjen meg a tömeges végrehajtó .NET API-val és az SDK-val, beleértve a kiadási dátumokat, a kivonási dátumokat és a Azure Cosmos DB tömeges végrehajtó .NET SDK egyes verzióiban végrehajtott módosításokat.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 989d52e773ed01a8fd1f4d383c42d7628e196a60
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814721"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET tömeges végrehajtó könyvtár: Információk letöltése 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Leírás**| A .net tömeges végrehajtó függvénytár lehetővé teszi, hogy az ügyfélalkalmazások tömeges műveleteket végezzenek Azure Cosmos DB fiókokon. Ez a függvénytár BulkImport, BulkUpdate és BulkDelete névtereket biztosít. A BulkImport modul képes a dokumentumok egy optimalizált módon történő betöltésére, így a gyűjtemény számára kiépített átviteli sebesség a maximális mértékben felhasználható. A BulkUpdate modul a Azure Cosmos DB tárolókban lévő meglévő adatmennyiségeket javításokként tudja frissíteni. A BulkDelete modul képes a dokumentumok tömeges törlésére olyan optimalizált módon, hogy a gyűjteményhez kiépített átviteli sebesség a maximális mértékig legyen felhasználva.|
|**SDK letöltése**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **BulkExecutor-könyvtár a GitHubban**| [GitHubon](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-dokumentáció**|[.NET API dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Első lépések**|[Ismerkedés a tömeges végrehajtó függvénytár .NET SDK-val](bulk-executor-dot-net.md)|
| **Aktuális támogatott keretrendszer**| Microsoft .NET Framework 4.5.2, 4.6.1 és .NET Standard 2,0 |

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* A Graph tömeges végrehajtó támogatása a csúcspontok és élek élettartamának elfogadásához

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Kijavított egy problémát, amely kivételeket okozott a Azure Cosmos DB rugalmas skálázása során, ha átjáró módban fut. Ez a javítás az 1.4.1-es kiadással egyenértékű működést tesz lehetővé.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* BulkDelete-támogatás hozzáadva az SQL API-fiókokhoz a partíciós kulcs elfogadásához, a rekordok törléséhez. Ez a módosítás a 1.4.0-kiadásnak megfelelő működést tesz lehetővé.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* A .NET Standard 2,0-et támogató MongoBulkExecutor is beleértve. Ez a funkció lehetővé teszi, hogy az a 1.3.0-kiadásnak megfelelően működjön, és a .NET Standard 2,0-et a célként megadott keretrendszerként támogatja.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* A .NET Standard 2,0-as számú támogatott cél-keretrendszer lett hozzáadva, hogy a BulkExecutor-könyvtár működjön a .NET Core-alkalmazásokkal.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Frissítette a tömeges végrehajtót, hogy most a Azure Cosmos DB .NET SDK (2.4.0) legújabb verzióját használja.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* A Graph tömeges végrehajtó támogatása a csúcspontok és élek élettartamának elfogadásához

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Kijavított egy problémát, amely kivételeket okozott a Azure Cosmos DB rugalmas skálázása során, ha átjáró módban fut.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* BulkDelete-támogatás hozzáadva az SQL API-fiókokhoz a partíciós kulcs elfogadásához, a rekordok törléséhez.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Kijavított egy problémát, amely a BulkExecutor által használt felhasználói ügynök formázási hibáját okozta.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Továbbfejlesztettük a BulkExecutor importálását és frissítését az API-k számára, hogy transzparens módon alkalmazkodjon Cosmos DB tároló rugalmas méretezéséhez, ha a tárterület meghaladja a jelenlegi kapacitást a kivételek nélkül.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* A DocumentDB .NET SDK függőségének összeütközött a 2.1.3-es verzióra.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Kijavított egy problémát, amely miatt a BulkExecutor JSRT hibát okozott a rögzített gyűjteményekbe való importálás során.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* A BulkDelete művelet támogatása a Azure Cosmos DB SQL API-fiókokhoz.
* A BulkImport művelet támogatása a Azure Cosmos DB API-MongoDB rendelkező fiókokhoz.
* A DocumentDB .NET SDK függőségének a 2.0.0 verziójára ütközött. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2-es

* A BulkImport művelet támogatása a Azure Cosmos DB Gremlin API-fiókokhoz.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Kisebb hibajavítás a BulkImport művelethez Azure Cosmos DB SQL API-fiókokhoz.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* A BulkImport-és BulkUpdate-műveletek támogatása a Azure Cosmos DB SQL API-fiókokhoz.

## <a name="next-steps"></a>További lépések

A tömeges végrehajtó Java-függvénytárat a következő cikkben tekintheti meg:

[Java tömeges végrehajtó könyvtár SDK és kiadási információk](sql-api-sdk-bulk-executor-java.md)
