---
title: Azure üzenetsor-tárolási trigger és kötések Azure Functions – áttekintés
description: Ismerje meg, hogyan használható az Azure üzenetsor-tároló-trigger és a kimeneti kötés a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277309"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure üzenetsor-tárolási trigger és kötések Azure Functions – áttekintés

Azure Functions futtatható új Azure üzenetsor-tároló üzenetekkel, és egy függvényen belül is írhat üzenetsor-üzeneteket.

| Műveletek | Típus |
|---------|---------|
| Függvény futtatása üzenetsor-tárolási adatváltozásként | [Eseményindító](./functions-bindings-storage-queue-trigger.md) |
| Írási várólista tárolási üzenetei |[Kimeneti kötés](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

### <a name="functions-2x-and-higher"></a>2\. x és újabb függvények

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet-csomag], 3. x verziójának telepítése | |
| C#Parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) használata ajánlott a Visual Studio Code használatával. |
| C#Parancsfájl (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [Bővítmények frissítése]című témakört. |

[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Bővítmények frissítése]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A functions 1. x alkalmazások automatikusan hivatkoznak a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomagra, 2. x verzióra.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása üzenetsor-tárolási adatváltozásként (trigger)](./functions-bindings-storage-queue-trigger.md)
- [Írási várólista tárolási üzenetei (kimeneti kötés)](./functions-bindings-storage-queue-output.md)
