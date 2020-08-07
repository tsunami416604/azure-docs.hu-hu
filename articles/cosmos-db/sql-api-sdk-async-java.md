---
title: 'Azure Cosmos DB: SQL aszinkron Java API, SDK &-erőforrások'
description: Ismerkedjen meg az SQL aszinkron Java API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 3f7b6c46a69acf8eea546469cf7919e86703f17c
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852684"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB aszinkron Java SDK az SQL API-hoz: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Spark-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * Rest (/rest/api
> * [REST erőforrás-szolgáltató](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

Az SQL API aszinkron Java SDK eltér az SQL API Java SDK-val, mivel aszinkron műveleteket biztosít a [nettó könyvtár](https://netty.io/)támogatásával. A korábban létező [SQL API Java SDK](sql-api-sdk-java.md) nem támogatja az aszinkron műveleteket. 

> [!IMPORTANT]  
> Ez *nem* a legújabb Java SDK a Azure Cosmos db! Vegye fontolóra [Azure Cosmos db Java SDK v4](sql-api-sdk-java-v4.md) használatát a projekthez. A frissítéshez kövesse az [áttelepítés Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -útmutató és a [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) útmutató utasításait. 
>

| |  |
|---|---|
| **SDK letöltése** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-dokumentáció** |[Java API-referenciák dokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Közreműködés az SDK-val** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Első lépések** | [Ismerkedés az aszinkron Java SDK-val](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Kódminta** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Teljesítménnyel kapcsolatos tippek**| [GitHub-információ](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimális támogatott futtatókörnyezet**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>További információ
További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja.

