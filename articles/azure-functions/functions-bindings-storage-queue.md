---
title: Az Azure Queue storage eseményindítója és kötései az Azure Functionshez – áttekintés
description: Ismerje meg, hogyan használhatja az Azure Queue storage eseményindítóés kimeneti kötés az Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277309"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Az Azure Queue storage eseményindítója és kötései az Azure Functionshez – áttekintés

Az Azure Functions új Azure Queue storage-üzenetek létrehozásakor futtatható, és egy függvényen belül is írhat várólistaüzeneteket.

| Műveletek | Típus |
|---------|---------|
| Függvény futtatása várólista-tárolási adatok módosításaként | [Eseményindító](./functions-bindings-storage-queue-trigger.md) |
| Várólista tárolási üzenetének írása |[Kimenetkötés](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Hozzáadás a Functions alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Funkciók 2.x és magasabb

Az eseményindító és a kötések használatához hivatkozni kell a megfelelő csomagra. A NuGet csomag .NET osztálytárakhoz használatos, míg a bővítményköteg az összes többi alkalmazástípushoz használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet csomag]telepítése 3.x verzió | |
| C# Script, Java, JavaScript, Python, PowerShell | A [bővítménycsomag regisztrálása]          | Az [Azure Tools bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) használata ajánlott a Visual Studio-kód használata. |
| C# Script (csak online az Azure Portalon)         | Kötés hozzáadása                            | Ha a meglévő kötésbővítményeket a függvényalkalmazás újbóli közzététele nélkül szeretné frissíteni, olvassa el [a Bővítmények frissítése (Bővítmények frissítése) (Bővítmények frissítése) (Bővítmények frissítése) (Bővítmények frissítése) témakört.] |

[core tools]: ./functions-run-local.md
[bővítőcsomag]: ./functions-bindings-register.md#extension-bundles
[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[A bővítmények frissítése]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A Functions 1.x alkalmazások automatikusan rendelkeznek egy hivatkozással a [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomag 2.x-es verziójával.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>További lépések

- [Függvény futtatása várólista-tárolási adatok változásakor (Eseményindító)](./functions-bindings-storage-queue-trigger.md)
- [Várólista-tárolási üzenetek írása (Kimenetkötés)](./functions-bindings-storage-queue-output.md)
