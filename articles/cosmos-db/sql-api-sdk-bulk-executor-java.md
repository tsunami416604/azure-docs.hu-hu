---
title: 'Azure Cosmos DB: tömeges végrehajtó Java API, SDK &-erőforrások'
description: Ismerkedjen meg a tömeges végrehajtó Java API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB tömeges végrehajtó Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 6fe075d2679ec13217200bc8b0da132e8f4d9b57
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660440"
---
# <a name="java-bulk-executor-library-download-information"></a>Java tömeges végrehajtó könyvtár: letöltési információ

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

| |  |
|---|---|
|**Leírás**|A tömeges végrehajtó függvénytár lehetővé teszi, hogy az ügyfélalkalmazások tömeges műveleteket végezzenek Azure Cosmos DB fiókokban. a tömeges végrehajtó függvénytár BulkImport és BulkUpdate névtereket biztosít. A BulkImport modul képes a dokumentumok egy optimalizált módon történő betöltésére, így a gyűjtemény számára kiépített átviteli sebesség a maximális mértékben felhasználható. A BulkUpdate modul a meglévő, az Azure Cosmos-tárolókban tárolt adatmennyiséget javításként is frissítheti.|
|**SDK letöltése**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Tömeges végrehajtó könyvtár a GitHubban**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-dokumentáció**| [Java API-referenciák dokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Bevezetés**|[Ismerkedés a tömeges végrehajtó függvénytár Java SDK-val](bulk-executor-java.md)|
|**Minimális támogatott futtatókörnyezet**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Javítsa ki a DocumentAnalyzer. Java fájlt, hogy megfelelően kinyerje a beágyazott partíciós kulcs értékeit a JSON-ből.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Adja hozzá a BulkDelete műveletek funkcióit, és próbálkozzon újra a hibákkal, és adja vissza azon hibák listáját, amelyeket újra lehet próbálni.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* A Cosmos SDK verziójának 2.4.7 frissítése.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Javítsa ki a "mergeAll" parancsot a "id" és a "Partition Key" érték folytatásához, hogy a rendszer az "id" és a "Partition Key" érték után elhelyezett javított dokumentum-tulajdonságokat hozzáadja a frissített elemek listájához.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Frissítse a párhuzamossági fokot 1 értékre, és adja hozzá a hibakeresési naplókat a minibatch.


