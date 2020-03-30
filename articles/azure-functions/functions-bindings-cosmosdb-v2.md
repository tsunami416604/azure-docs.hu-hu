---
title: Az Azure Cosmos DB kötései a Functions 2.x-hez
description: Ismerje meg, hogyan használhatja az Azure Cosmos DB eseményindítók és kötések az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605758"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Az Azure Cosmos DB eseményindítója és kötései az Azure Functions 2.x számára – áttekintés

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Functions futásidejű verzióját: "]
> * [1-es verzió](functions-bindings-cosmosdb.md)
> * [2-es verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikkkészlet bemutatja, hogyan dolgozhat az [Azure Cosmos](../cosmos-db/serverless-computing-database.md) DB-kötésekkel az Azure Functions 2.x-ben. Az Azure Functions támogatja az Azure Cosmos DB eseményindító, bemeneti és kimeneti kötéseit.

| Műveletek | Típus |
|---------|---------|
| Egy függvény futtatása, ha egy Azure Cosmos DB-dokumentumot hoz létre vagy módosít | [Eseményindító](./functions-bindings-cosmosdb-v2-trigger.md) |
| Azure Cosmos DB-dokumentum olvasása | [Bemeneti kötés](./functions-bindings-cosmosdb-v2-input.md) |
| Az Azure Cosmos DB-dokumentum módosítási helyének mentése  |[Kimenetkötés](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Ez a hivatkozás az [Azure Functions 2.x-es verziójára vonatkozik.](functions-versions.md)  A kötések functions 1.x-ben való használatáról az [Azure Functions 1.x Azure Cosmos DB-kötései](functions-bindings-cosmosdb.md)című témakörben talál tájékoztatást.
>
> Ez a kötés eredetileg DocumentDB volt. A Functions 2.x-es verziójában az eseményindító, a kötések és a csomag neve Cosmos DB.

## <a name="supported-apis"></a>Támogatott API-k

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Hozzáadás a Functions alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Funkciók 2.x és magasabb

Az eseményindító és a kötések használatához hivatkozni kell a megfelelő csomagra. A NuGet csomag .NET osztálytárakhoz használatos, míg a bővítményköteg az összes többi alkalmazástípushoz használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet csomag]telepítése 3.x verzió | |
| C# Script, Java, JavaScript, Python, PowerShell | A [bővítménycsomag regisztrálása]          | Az [Azure Tools bővítmény] használata ajánlott a Visual Studio-kód használata. |
| C# Script (csak online az Azure Portalon)         | Kötés hozzáadása                            | Ha a meglévő kötésbővítményeket a függvényalkalmazás újbóli közzététele nélkül szeretné frissíteni, olvassa el [a Bővítmények frissítése (Bővítmények frissítése) (Bővítmények frissítése) (Bővítmények frissítése) (Bővítmények frissítése) témakört.] |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[bővítőcsomag]: ./functions-bindings-register.md#extension-bundles
[A bővítmények frissítése]: ./install-update-binding-extensions-manual.md
[Az Azure Tools bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A Functions 1.x alkalmazások automatikusan rendelkeznek egy hivatkozással a [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomag 2.x-es verziójával.

## <a name="next-steps"></a>További lépések

- [Egy függvény futtatása, ha egy Azure Cosmos DB-dokumentum jön létre vagy módosul (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB-dokumentum olvasása (bemeneti kötés)](./functions-bindings-cosmosdb-v2-input.md)
- [Az Azure Cosmos DB-dokumentum módosításának mentése (kimeneti kötés)](./functions-bindings-cosmosdb-v2-output.md)
