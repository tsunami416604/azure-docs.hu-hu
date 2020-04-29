---
title: 'Azure Cosmos DB: SQL aszinkron Java API, SDK &-erőforrások'
description: Ismerkedjen meg az SQL aszinkron Java API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 7dad1e929c2beb32605f67219ed30d2fd9a3a394
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985473"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB aszinkron Java SDK az SQL API-hoz: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

Az SQL API aszinkron Java SDK eltér az SQL API Java SDK-val, mivel aszinkron műveleteket biztosít a [nettó könyvtár](https://netty.io/)támogatásával. A korábban létező [SQL API Java SDK](sql-api-sdk-java.md) nem támogatja az aszinkron műveleteket. 

| |  |
|---|---|
| **SDK letöltése** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-dokumentáció** |[Java API-referenciák dokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Közreműködés az SDK-val** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Bevezetés** | [Ismerkedés az aszinkron Java SDK-val](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Kódminta** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Teljesítménnyel kapcsolatos tippek**| [GitHub-információ](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimális támogatott futtatókörnyezet**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja.

