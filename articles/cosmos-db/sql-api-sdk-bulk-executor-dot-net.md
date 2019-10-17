---
title: 'Azure Cosmos DB: tömeges végrehajtó .NET API, SDK &-erőforrások'
description: Ismerkedjen meg a tömeges végrehajtó .NET API-val és az SDK-val, beleértve a kiadási dátumokat, a kivonási dátumokat és a Azure Cosmos DB tömeges végrehajtó .NET SDK egyes verzióiban végrehajtott módosításokat.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/11/2019
ms.author: ramkris
ms.openlocfilehash: 9da994550609b0be44362e0f0059844b288aabbd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388064"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET tömeges végrehajtó könyvtár: letöltési információ 

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
| **Leírás**| A .net tömeges végrehajtó függvénytár lehetővé teszi, hogy az ügyfélalkalmazások tömeges műveleteket végezzenek Azure Cosmos DB fiókokon. Ez a függvénytár BulkImport, BulkUpdate és BulkDelete névtereket biztosít. A BulkImport modul képes a dokumentumok egy optimalizált módon történő betöltésére, így a gyűjtemény számára kiépített átviteli sebesség a maximális mértékben felhasználható. A BulkUpdate modul a meglévő, az Azure Cosmos-tárolókban tárolt adatmennyiséget javításként is frissítheti. A BulkDelete modul képes a dokumentumok tömeges törlésére olyan optimalizált módon, hogy a gyűjteményhez kiépített átviteli sebesség a maximális mértékig legyen felhasználva.|
|**SDK letöltése**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Tömeges végrehajtó könyvtár a GitHubban**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-dokumentáció**|[.NET API-referenciák dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Első lépések**|[Ismerkedés a tömeges végrehajtó függvénytár .NET SDK-val](bulk-executor-dot-net.md)|
| **Jelenleg támogatott keretrendszer**| Microsoft .NET Framework 4.5.2, 4.6.1 és .NET Standard 2,0 |

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name241-preview241-preview"></a>@no__t – 02.4.1 – előzetes verzió

* A BulkDelete válaszában rögzített TotalElapsedTime, hogy megfelelően mérje a teljes időt, beleértve az újrapróbálkozásokat is.

### <a name="a-name240-preview240-preview"></a>@no__t – 02.4.0 – előzetes verzió

* Módosult az SDK-függőség a következőre: > = 2.5.1

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-Preview2

* A Graph tömeges végrehajtó támogatása a csúcspontok és élek élettartamának elfogadásához

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-Preview2

* Kijavított egy problémát, amely kivételeket okozott a Azure Cosmos DB rugalmas skálázása során, ha átjáró módban fut. Ez a javítás az 1.4.1-es kiadással egyenértékű működést tesz lehetővé.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-Preview2

* BulkDelete-támogatás hozzáadva az SQL API-fiókokhoz a partíciós kulcs elfogadásához, a rekordok törléséhez. Ez a módosítás a 1.4.0-kiadásnak megfelelő működést tesz lehetővé.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-Preview2

* A .NET Standard 2,0-et támogató MongoBulkExecutor is beleértve. Ez a funkció lehetővé teszi, hogy az a 1.3.0-kiadásnak megfelelően működjön, és a .NET Standard 2,0-et a célként megadott keretrendszerként támogatja.

### <a name="a-name200-preview200-preview"></a>@no__t – 02.0.0 – előzetes verzió

* A .NET Standard 2,0 a támogatott cél keretrendszerek egyike, hogy a tömeges végrehajtó függvénytár működjön a .NET Core-alkalmazásokkal.

### <a name="a-name184184"></a>@no__t – 01.8.4

* Az azonosítók rögzített kezelése a BulkImport hívásakor numerikus értékkel rendelkező POCO objektumok listájával.

### <a name="a-name183183"></a>@no__t – 01.8.3

* A BulkDelete válaszában rögzített TotalElapsedTime, hogy megfelelően mérje a teljes időt, beleértve az újrapróbálkozásokat is.

### <a name="a-name182182"></a>@no__t – 01.8.2

* Rögzített magas CPU-felhasználás bizonyos helyzetekben.
* A nyomkövetés mostantól a TraceSource-t használja. A felhasználók megadhatnak figyelőket a `BulkExecutorTrace` forráshoz.
* Rögzített egy ritka forgatókönyv, amely zárolást okozhat a dokumentumok 2 MB-os méretének közelében történő küldésekor.

### <a name="a-name160160"></a>@no__t – 01.6.0

* Frissítette a tömeges végrehajtót, hogy most a Azure Cosmos DB .NET SDK (2.4.0) legújabb verzióját használja.

### <a name="a-name150150"></a>@no__t – 01.5.0

* A Graph tömeges végrehajtó támogatása a csúcspontok és élek élettartamának elfogadásához

### <a name="a-name141141"></a>@no__t – 01.4.1

* Kijavított egy problémát, amely kivételeket okozott a Azure Cosmos DB rugalmas skálázása során, ha átjáró módban fut.

### <a name="a-name140140"></a>@no__t – 01.4.0

* BulkDelete-támogatás hozzáadva az SQL API-fiókokhoz a partíciós kulcs elfogadásához, a rekordok törléséhez.

### <a name="a-name130130"></a>@no__t – 01.3.0

* Kijavított egy problémát, amely a tömeges végrehajtó által használt felhasználói ügynök formázási hibáját okozta.

### <a name="a-name120120"></a>@no__t – 01.2.0

* Továbbfejlesztettük a tömeges végrehajtó importálási és frissítési API-kat, hogy transzparens módon igazodjanak a Cosmos-tároló rugalmas skálázásához, ha a tárterület meghaladja a jelenlegi kapacitást a kivételek nélkül.

### <a name="a-name112112"></a>@no__t – 01.1.2

* A DocumentDB .NET SDK függőségének összeütközött a 2.1.3-es verzióra.

### <a name="a-name111111"></a>@no__t – 01.1.1

* Kijavított egy problémát, amely a tömeges végrehajtót okozta, hogy JSRT hibát okozott a rögzített gyűjteményekbe való importálás során.

### <a name="a-name110110"></a>@no__t – 01.1.0

* A BulkDelete művelet támogatása a Azure Cosmos DB SQL API-fiókokhoz.
* A BulkImport művelet támogatása a Azure Cosmos DB API-MongoDB rendelkező fiókokhoz.
* A DocumentDB .NET SDK függőségének a 2.0.0 verziójára ütközött. 

### <a name="a-name102102"></a>@no__t – 01.0.2

* A BulkImport művelet támogatása a Azure Cosmos DB Gremlin API-fiókokhoz.

### <a name="a-name101101"></a>@no__t – 01.0.1

* Kisebb hibajavítás a BulkImport művelethez Azure Cosmos DB SQL API-fiókokhoz.

### <a name="a-name100100"></a>@no__t – 01.0.0

* A BulkImport-és BulkUpdate-műveletek támogatása a Azure Cosmos DB SQL API-fiókokhoz.

## <a name="next-steps"></a>Következő lépések

A tömeges végrehajtó Java-függvénytárat a következő cikkben tekintheti meg:

[Java tömeges végrehajtó könyvtár SDK és kiadási információk](sql-api-sdk-bulk-executor-java.md)
