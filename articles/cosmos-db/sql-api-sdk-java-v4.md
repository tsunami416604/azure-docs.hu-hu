---
title: Azure Cosmos DB Java SDK v4 az SQL API kibocsátási megjegyzésekhez és erőforrásokhoz
description: Ismerkedjen meg a Azure Cosmos DB Java SDK v4 for SQL API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 686c1e37e093a358bb5122fbfeccdfd98eb301ce
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804231"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring-adatv2](sql-api-sdk-java-spring-v2.md)
> * [Spring-adatforrások v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

Az Azure Cosmos DB Java SDK v4 for Core (SQL) egy aszinkron API-t és egy "Sync" API-t egyesít egy Maven-összetevőbe. A v4 SDK továbbfejlesztett teljesítményt, új API-szolgáltatásokat és aszinkron támogatást biztosít a projekt-reaktor és a [nettó könyvtár](https://netty.io/)alapján. A felhasználók a Azure Cosmos DB Java SDK v4 és a [Azure Cosmos db aszinkron Java SDK v2](sql-api-sdk-async-java.md) és a [Azure Cosmos db Sync Java SDK v2](sql-api-sdk-java.md)használatával is jobb teljesítményt számíthatnak.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések csak Azure Cosmos DB Java SDK v4-re vonatkoznak. Ha jelenleg a v4-nél régebbi verziót használ, tekintse meg a következőt: [migrálás Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -re – útmutató a v4-re való frissítéshez.
>
> Íme három lépés a gyors kezdéshez!
> 1. Telepítse a [minimális támogatott Java-futtatókörnyezetet, a JDK 8-as verzióját,](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) hogy használhassa az SDK-t.
> 2. A [Azure Cosmos db Java SDK v4-hez készült gyors útmutatóban](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) megismerheti a Maven-összetevők elérését, és végigvezeti az alapszintű Azure Cosmos db kérelmeken.
> 3. Az alkalmazáshoz tartozó SDK optimalizálásához olvassa el a Azure Cosmos DB Java SDK v4 [teljesítményével kapcsolatos tippeket](performance-tips-java-sdk-v4-sql.md) és [hibaelhárítási](troubleshoot-java-sdk-v4-sql.md) útmutatókat.
>
> A [Azure Cosmos db Workshops és Labs](https://aka.ms/cosmosworkshop) egy másik nagyszerű erőforrás a Azure Cosmos db Java SDK v4 használatának megismeréséhez.
>

## <a name="helpful-content"></a>Hasznos tartalom

| Tartalom | Hivatkozás |
|---|---|
|**SDK letöltése**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-dokumentáció** | [Java API-referenciák dokumentációja](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable&preserve-view=true) |
|**Közreműködés az SDK-val** | [Azure SDK a Java központi tárházhoz a GitHubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Első lépések** | [Gyors útmutató: Java-alkalmazás létrehozása Azure Cosmos DB SQL API-beli adatkezeléshez](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [GitHub-tárház a gyors üzembe helyezési kóddal](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Alapszintű kódok** | [Azure Cosmos DB: Java-példák az SQL API-hoz](sql-api-java-sdk-samples.md) <br> [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konzolos alkalmazás változási hírcsatornával**| [Az adatcsatorna módosítása – Java SDK v4 minta](create-sql-api-java-changefeed.md) <br> [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Webalkalmazás mintája**| [Webalkalmazás létrehozása Java SDK v4 használatával](sql-api-java-application.md) <br> [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Teljesítménnyel kapcsolatos tippek**| [Teljesítménnyel kapcsolatos tippek Java SDK v4-hez](performance-tips-java-sdk-v4-sql.md)| 
| **Hibaelhárítás** | [Java SDK v4-hibák elhárítása](troubleshoot-java-sdk-v4-sql.md) |
| **Migrálás a v4-re egy régebbi SDK-ból** | [Migrálás Java V4 SDK-ba](migrate-java-v4-sdk.md) |
| **Minimális támogatott futtatókörnyezet**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 
| **Azure Cosmos DB workshopok és Labs** |[Cosmos DB workshopok kezdőlapja](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>További lépések
További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja.