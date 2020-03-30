---
title: 'Azure Cosmos DB: SQL Async Java API, SDK & erőforrások'
description: Tudjon meg mindent az SQL Async Java API-ról és az SDK-ról, beleértve a kiadási dátumokat, a megszüntetési dátumokat és az Azure Cosmos DB SQL Async Java SDK egyes verziói között végrehajtott módosításokat.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 05f597093c27f84d7f20cf0abd5858f44645b88d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73574920"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK SQL API-hoz: Kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Többi](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó - Java](sql-api-sdk-bulk-executor-java.md)

Az SQL API Async Java SDK a [Netty könyvtár](https://netty.io/)támogatásával különbözik az SQL API Java SDK-tól. A már meglévő [SQL API Java SDK](sql-api-sdk-java.md) nem támogatja az aszinkron műveleteket. 

| |  |
|---|---|
| **SDK letöltése** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-dokumentáció** |[Java API referenciadokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Hozzájárulás az SDK-hoz** | [Github](https://github.com/Azure/azure-cosmosdb-java) | 
|**Első lépések** | [Az Async Java SDK – első lépések](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Kódminta** | [Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Teljesítménnyel kapcsolatos tippek**| [GitHub-olvasás](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimálisan támogatott futásidő**|[JDK, 8.](https://aka.ms/azure-jdks) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB szolgáltatásról a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatáslapján olvashat bővebben.

