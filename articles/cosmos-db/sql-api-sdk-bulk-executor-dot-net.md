---
title: 'Az Azure Cosmos DB: tömeges végrehajtó .NET API SDK & erőforrások |} Microsoft Docs'
description: Tudnivalók a tömeges végrehajtó .NET API és az SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és az Azure Cosmos DB tömeges végrehajtó .NET SDK verziói között végrehajtott módosításokat.
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 5f2bc50a59219a38c2fcf4d501de4d8a11f6fbf1
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300754"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET tömeges végrehajtó könyvtár: adatok letöltése 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [tömeges végrehajtó - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [tömeges végrehajtó - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Leírás**</td><td>A tömeges végrehajtó library lehetővé teszi, hogy az ügyfélalkalmazások Azure Cosmos DB fiókok tömeges műveletek végrehajtásához. tömeges végrehajtó kódtár biztosít BulkImport, és BulkUpdate névtereket. A modul tömegesen BulkImport úgy, hogy egy gyűjtemény kiosztott átviteli sebesség a legnagyobb mértékben felhasznált betöltési optimalizált módon dokumentumokat. A modul tömegesen BulkUpdate frissíti a meglévő adatok Azure Cosmos DB-tárolókban lévő javítások.</td></tr>

<tr><td>**SDK letöltése**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**A Githubon BulkExecutor könyvtár**</td><td>[GitHubon](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**API-JÁNAK dokumentációja**</td><td>[.NET API referenciadokumentációt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Első lépések**</td><td>[A tömeges végrehajtó library .NET SDK használatába](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Aktuális támogatott keretrendszer**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(verzió > = 1.21.1)</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(verzió > = 10.0.2)
</li></ul></td></tr>
</table></br>

