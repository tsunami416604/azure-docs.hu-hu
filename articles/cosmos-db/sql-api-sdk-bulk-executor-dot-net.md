---
title: 'Az Azure Cosmos DB: Tömeges végrehajtó .NET API, SDK és -erőforrások |} A Microsoft Docs'
description: Mindent megtudhat a tömeges végrehajtó .NET API-t és az SDK kiadási dátum, kivezetési dátum és az Azure Cosmos DB tömeges végrehajtó .NET SDK minden verziója közötti végzett módosításokat.
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/14/2018
ms.author: ramkris
ms.openlocfilehash: ffd8f438429cd8769ac0dbff7f489327166e0000
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294458"
---
# <a name="net-bulk-executor-library-download-information"></a>Tömeges végrehajtó .NET könyvtár: adatok letöltése 

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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [Tömeges végrehajtó – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Leírás**</td><td>A tömeges végrehajtó kódtár lehetővé teszi, hogy az ügyfélalkalmazások az Azure Cosmos DB-fiókokhoz tömeges műveletek végrehajtásához. Tömeges végrehajtó kódtár BulkImport, BulkUpdate és BulkDelete névterek biztosít. A modul tömegesen BulkImport dokumentumok betöltését optimalizált módon, hogy a legnagyobb mértékben használja fel a gyűjteményhez kiosztott átviteli sebesség. A modul tömegesen BulkUpdate frissíti a meglévő adatok az Azure Cosmos DB-tárolók javítások. A BulkDelete modul tömegesen delete dokumentumok optimalizált módon, hogy a legnagyobb mértékben használja fel a gyűjteményhez kiosztott átviteli sebesség.</td></tr>

<tr><td>**SDK letöltése**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**BulkExecutor könyvtár a Githubon**</td><td>[GitHubon](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**API-dokumentáció**</td><td>[.NET API dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Első lépések**</td><td>[A tömeges végrehajtó tár .NET SDK használatának első lépései](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Aktuális támogatott keretrendszer**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(verzió > = 2.0.0)</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(verzió > = 9.0.1)
</li></ul></td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Azure Cosmos DB SQL API-fiókok BulkDelete művelet támogatása.
* Azure Cosmos DB MongoDB API-fiókok BulkImport művelet támogatása.
* Bumped fel a DocumentDB .NET SDK 2.0.0-s verziójával függőséget. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2-es

* Azure Cosmos DB Gremlin API-fiókok BulkImport művelet támogatása.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Az Azure Cosmos DB SQL API-fiókok BulkImport művelet kisebb hibajavítás.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Azure Cosmos DB SQL API-fiókok BulkImport és BulkUpdate műveletek támogatása.
