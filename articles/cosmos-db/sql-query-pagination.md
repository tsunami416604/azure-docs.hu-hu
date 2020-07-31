---
title: Tördelés Azure Cosmos DB
description: Tudnivalók a lapozással kapcsolatos fogalmakról és a folytatólagos tokenekről
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 978cc67336fe7f6f89970007215da73da0737f08
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433580"
---
# <a name="pagination-in-azure-cosmos-db"></a>Tördelés Azure Cosmos DB

Azure Cosmos DB a lekérdezések több oldalnyi eredményt is tartalmazhatnak. Ez a dokumentum ismerteti a Azure Cosmos DB lekérdezési motorjának feltételeit, hogy eldöntse, hogy több oldalra kell-e bontani a lekérdezési eredményeket. A folytatási tokeneket igény szerint a több oldalra kiterjedő lekérdezési eredmények kezelésére is használhatja.

## <a name="understanding-query-executions"></a>A lekérdezés-végrehajtás ismertetése

Időnként a lekérdezési eredmények több oldalra lesznek felosztva. Az egyes lapok eredményeit külön lekérdezési végrehajtással hozza létre a rendszer. Ha a lekérdezési eredményeket egyetlen végrehajtással nem lehet visszaadni, Azure Cosmos DB automatikusan több oldalra osztja el az eredményeket.

A lekérdezés által visszaadott elemek maximális számát a következő beállításával adhatja meg: `MaxItemCount` . A a `MaxItemCount` kérelem szerint van megadva, és garantálja, hogy a lekérdezési motor az adott számú elemet vagy kevesebbet adja vissza. Beállíthatja `MaxItemCount` , hogy `-1` Ha nem szeretné korlátozni az eredmények számát a lekérdezés-végrehajtás során.

Emellett más okokból is előfordulhat, hogy a lekérdezési motornak több oldalra kell bontania a lekérdezési eredményeket. Ezek a következők:

- A tároló szabályozása megtörtént, és nem volt elérhető RUs további lekérdezési eredmények visszaadásához
- A lekérdezés-végrehajtás válasza túl nagy volt
- A lekérdezés végrehajtásának ideje túl hosszú volt.
- A lekérdezési motor hatékonyabban jeleníti meg az eredményeket további végrehajtások esetén

A lekérdezés végrehajtásakor visszaadott elemek száma mindig kisebb vagy egyenlő lesz `MaxItemCount` . Előfordulhat azonban, hogy más feltételek korlátozták a lekérdezés által visszaadott eredmények számát. Ha többször hajtja végre ugyanazt a lekérdezést, előfordulhat, hogy a lapok száma nem állandó. Ha például egy lekérdezés szabályozva van, előfordulhat, hogy a lapon kevesebb találat érhető el, ami azt jelenti, hogy a lekérdezés további lapokkal fog rendelkezni. Bizonyos esetekben előfordulhat, hogy a lekérdezés az eredmények üres oldalát is visszaadja.

## <a name="handling-multiple-pages-of-results"></a>Az eredmények több oldalának kezelésére

A lekérdezés pontos eredményeinek biztosítása érdekében az összes oldalt át kell haladnia. A lekérdezéseket továbbra is végre kell hajtania, amíg nincs további oldal.

Íme néhány példa a több oldallal rendelkező lekérdezések eredményeinek feldolgozására:

[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)

[Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)

[Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)

[Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Folytatási tokenek

A .NET SDK-ban és a Java SDK-ban igény szerint a folytatási tokeneket is használhatja könyvjelzőként a lekérdezés előrehaladásához. Azure Cosmos DB lekérdezés-végrehajtás állapot nélküli a kiszolgáló oldalán, és bármikor folytatható a folytatási token használatával. A folytatási tokenek nem támogatottak a Node.js SDK-ban vagy a Python SDK-ban.

Íme néhány példa a folytatási tokenek használatára:

[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)

[Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)

Ha a lekérdezés folytatási tokent ad vissza, akkor további lekérdezési eredmények is megtalálhatók.

Azure Cosmos DB REST API a folytatási tokeneket a `x-ms-continuation` fejléctel kezelheti. Mint a .NET vagy a Java SDK lekérdezésével, ha a `x-ms-continuation` Válasz fejléce nem üres, akkor a lekérdezés további eredményeket tartalmaz.

Ha ugyanazt az SDK-verziót használja, a folytatási tokenek soha nem járnak le. Lehetősége van a [folytatási token méretének korlátozására](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb?view=azure-dotnet#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb)is. Az adatmennyiségtől vagy a tároló fizikai partícióinak számától függetlenül a lekérdezések egyetlen folytatási tokent adnak vissza.

A folytatási tokenek nem használhatók a [Group By](sql-query-group-by.md) vagy [DISTINCT](sql-query-keywords.md#distinct) lekérdezésekhez, mert ezek a lekérdezések jelentős mennyiségű állapot tárolását igénylik. A alkalmazással végzett lekérdezésekhez használhatja `DISTINCT` a folytatási jogkivonatokat, ha hozzáadja `ORDER BY` a lekérdezéshez.

Az alábbi példa egy, a `DISTINCT` folytatási tokent használó lekérdezésre mutat:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Következő lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY záradék](sql-query-order-by.md)