---
title: Áttelepítés a tömeges végrehajtó könyvtárról az Azure Cosmos DB .NET V3 SDK tömeges támogatására
description: Ismerje meg, hogyan telepítheti át az alkalmazást a tömeges végrehajtó könyvtár ból a tömeges támogatás az Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: maquaran
ms.openlocfilehash: e1a2a5d849d3c94d62b8645c41f288ba130aa6a4
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479330"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Áttelepítés a tömeges végrehajtó könyvtárról az Azure Cosmos DB .NET V3 SDK tömeges támogatására

Ez a cikk a [.](bulk-executor-dot-net.md) [bulk support](tutorial-sql-api-dotnet-bulk-import.md)

## <a name="enable-bulk-support"></a>Tömeges támogatás engedélyezése

Engedélyezze a `CosmosClient` tömeges támogatást a példányon az [AllowBulkExecution](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) konfiguráción keresztül:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Feladatok létrehozása minden művelethez

A . [Task Parallel Library](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) 

Nincs egyetlen módszer, amely a dokumentumok vagy műveletek listáját bemeneti paraméterként fogja figyelembe, hanem létre kell hoznia egy feladatot minden tömegesen végrehajtani kívánt művelethez.

Ha például a kezdeti bevitel olyan elemek listája, amelyekben az egyes elemek a következő sémával rendelkeznek:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Ha tömeges importálást szeretne végezni (hasonlóan a BulkExecutor.BulkImportAsync fájlhoz), `CreateItemAsync` minden elemértékhez egyidejű hívásokat kell végrehajtania. Példa:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Ha tömeges *frissítést* szeretne végezni (hasonlóan a [BulkExecutor.BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)fájlhoz), `ReplaceItemAsync` az elemérték frissítése után egyidejű hívásokat kell végrehajtania a metódushoz. Példa:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

És ha azt szeretnénk, hogy tömeges *törlése* (hasonlóan a [BulkExecutor.BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), meg kell, hogy egyidejű `DeleteItemAsync`hívásokat , a és a `id` partíció kulcs minden elem. Példa:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Feladat eredményállapotának rögzítése

Az előző kódpéldákban létrehozott egy egyidejű tevékenységlistát, `CaptureOperationResponse` és meghívta a metódust az egyes tevékenységekhez. Ez a módszer egy olyan bővítmény, amely lehetővé teszi számunkra, hogy fenntartsák a *hasonló válasz séma,* mint BulkExecutor, a hibák rögzítésével és nyomon követése a [kérelem egységek használatát](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Amennyiben `OperationResponse` a következőképpen nyilatkoznak:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Műveletek egyidejű végrehajtása

A feladatok listájának definiálása után várjon, amíg mind befejeződnek. A feladatok befejezését a tömeges művelet hatókörének meghatározásával követheti nyomon, ahogy az a következő kódrészletben látható:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Adatok rögzítése

Az előző kód megvárja, amíg az összes művelet befejeződik, és kiszámítja a szükséges statisztikákat. Ezek a statisztikák hasonlóak a tömeges végrehajtó könyvtár [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

A `BulkOperationResponse` következőket tartalmazza:

1. A műveletek listájának tömeges támogatáson keresztül történő feldolgozásához szükséges teljes idő.
1. A sikeres műveletek száma.
1. A felhasznált kérelemegységek összege.
1. Ha vannak hibák, megjeleníti a kivételt tartalmazó tuples listáját, valamint a kapcsolódó elemet naplózási és azonosítási célból.

## <a name="performance-improvements"></a>Teljesítménnyel kapcsolatos fejlesztések

A .NET SDK-val végzett egyéb műveletekhez ugyanúgy, mint a .NET SDK-val végzett egyéb műveletek, az adatfolyam API-k használata jobb teljesítményt eredményez, és elkerüli a szükségtelen szerializálást. 

Az adatfolyam API-k használata csak akkor lehetséges, ha a használt adatok jellege megegyezik a bájtfolyamok (például fájladatfolyamok) jellegével. Ilyen esetekben a `CreateItemStreamAsync` `ReplaceItemStreamAsync`, `DeleteItemStreamAsync` vagy metódusok `ResponseMessage` használata `ItemResponse`és a (helyett) való munka növeli az elérhető átviteli szintet.

## <a name="next-steps"></a>További lépések

* A .NET SDK-kiadásokról az [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) cikkében olvashat bővebben.
* A teljes [áttelepítési forráskód](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) beszereznie a GitHubról.
* [További tömeges minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
