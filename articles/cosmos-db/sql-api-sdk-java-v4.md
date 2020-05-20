---
title: Azure Cosmos DB Java SDK v4 az SQL API kibocsátási megjegyzésekhez és erőforrásokhoz
description: Ismerkedjen meg a Azure Cosmos DB Java SDK v4 for SQL API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: cdbb6d0f26b6a999d289e7e0ddbd8cb45dac8e47
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665200"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

Az Azure Cosmos DB Java SDK v4 for Core (SQL) egy aszinkron API-t és egy "Sync" API-t egyesít egy Maven-összetevőbe. A v4 SDK továbbfejlesztett teljesítményt, új API-szolgáltatásokat és aszinkron támogatást biztosít a projekt-reaktor és a [nettó könyvtár](https://netty.io/)alapján. A felhasználók a Azure Cosmos DB Java SDK v4 és a [Azure Cosmos db aszinkron Java SDK v2](sql-api-sdk-async-java.md) és a [Azure Cosmos db Sync Java SDK v2](sql-api-sdk-java.md)használatával is jobb teljesítményt számíthatnak.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések csak Azure Cosmos DB Java SDK v4-re vonatkoznak. Ha jelenleg a v4-nél régebbi verziót használ, tekintse meg a következőt: [migrálás Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -re – útmutató a v4-re való frissítéshez.
>
> Íme három lépés a gyors kezdéshez!
> 1. Telepítse a [minimális támogatott Java-futtatókörnyezetet, a JDK 8-as verzióját,](/java/azure/jdk/?view=azure-java-stable) hogy használhassa az SDK-t.
> 2. A [Azure Cosmos db Java SDK v4-hez készült gyors útmutatóban](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) megismerheti a Maven-összetevők elérését, és végigvezeti az alapszintű Azure Cosmos db kérelmeken.
> 3. Az alkalmazáshoz tartozó SDK optimalizálásához olvassa el a Azure Cosmos DB Java SDK v4 [teljesítményével kapcsolatos tippeket](performance-tips-java-sdk-v4-sql.md) és [hibaelhárítási](troubleshoot-java-sdk-v4-sql.md) útmutatókat.
>
> A [Azure Cosmos db Workshops és Labs](https://aka.ms/cosmosworkshop) egy másik nagyszerű erőforrás a Azure Cosmos db Java SDK v4 használatának megismeréséhez.
>

| |  |
|---|---|
| **SDK letöltése** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-dokumentáció** |Java API-referenciák dokumentációja|
|**Közreműködés az SDK-val** | [Azure SDK a Java központi tárházhoz a GitHubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Bevezetés** | Gyors útmutató [: Java-alkalmazás létrehozása Azure Cosmos db SQL API-beli adatkezelési szolgáltatásokhoz](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [GitHub](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) -tárház a gyors üzembe helyezési kóddal | 
|**Alapszintű kódok** | Azure Cosmos DB: Java-példák az SQL API-ra · [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konzolos alkalmazás változási hírcsatornával**| [Change feed-Java SDK v4 minta](create-sql-api-java-changefeed.md) · [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Webalkalmazás mintája**| [Webalkalmazás létrehozása Java SDK v4](sql-api-java-application.md) használatával [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Teljesítménnyel kapcsolatos tippek**| [Teljesítménnyel kapcsolatos tippek Java SDK v4-hez](performance-tips-java-sdk-v4-sql.md)| 
| **Hibaelhárítás** | [Java SDK v4-hibák elhárítása](troubleshoot-java-sdk-v4-sql.md) |
| **Migrálás a v4-re egy régebbi SDK-ból** | [Migrálás Java V4 SDK-ba](migrate-java-v4-sdk.md) |
| **Minimális támogatott futtatókörnyezet**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB workshopok és Labs** |[Cosmos DB workshopok kezdőlapja](https://aka.ms/cosmosworkshop)

