---
title: 'Az Azure Cosmos DB: tömeges végrehajtó .NET API SDK & erőforrások |} Microsoft Docs'
description: Tudnivalók a tömeges végrehajtó .NET API és az SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és az Azure Cosmos DB tömeges végrehajtó .NET SDK verziói között végrehajtott módosításokat.
services: cosmos-db
documentationcenter: .net
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 52dcd8497a6dad2885a27197f873de4603378501
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="net-bulkexecutor-library-download-information"></a>.NET BulkExecutor könyvtár: adatok letöltése 

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
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Leírás**</td><td>A BulkExecutor könyvtár ügyfélalkalmazások teszi lehetővé az Azure Cosmos DB esetében tömeges műveletek végrehajtásához. BulkExecutor kódtár biztosít BulkImport, és BulkUpdate névtereket. A modul tömegesen BulkImport úgy, hogy egy gyűjtemény kiosztott átviteli sebesség a legnagyobb mértékben felhasznált betöltési optimalizált módon dokumentumokat. A modul tömegesen BulkUpdate frissíti a meglévő adatok Azure Cosmos DB-tárolókban lévő javítások.</td></tr>

<tr><td>**SDK letöltése**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**A Githubon BulkExecutor könyvtár**</td><td>[GitHubon](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**API-JÁNAK dokumentációja**</td><td>[.NET API referenciadokumentációt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Első lépések**</td><td>[Ismerkedjen meg a BulkExecutor library .NET SDK-val](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Aktuális támogatott keretrendszer**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(verzió > = 1.21.1)</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(verzió > = 10.0.2)
</li></ul></td></tr>
</table></br>

