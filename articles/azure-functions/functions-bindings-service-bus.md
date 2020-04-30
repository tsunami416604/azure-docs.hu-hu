---
title: Azure Functions-kötések Azure Service Bus
description: Megtudhatja, hogyan küldhet Azure Service Bus eseményindítókat és kötéseket a Azure Functionsban.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277413"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions-kötések Azure Service Bus

A Azure Functions a [Azure Service Bus](https://azure.microsoft.com/services/service-bus) az [Eseményindítók és kötések](./functions-triggers-bindings.md)használatával integrálható. A Service Bus integrálása lehetővé teszi olyan függvények összeállítását, amelyek reagálnak a várólistára vagy a témakör üzeneteire.

| Műveletek | Típus |
|---------|---------|
| Függvény futtatása Service Bus üzenetsor vagy témakör-üzenet létrehozásakor | [Eseményindító](./functions-bindings-service-bus-trigger.md) |
| Azure Service Bus üzenetek küldése |[Kimeneti kötés](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

### <a name="functions-2x-and-higher"></a>2. x és újabb függvények

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A 4. x verziójú [NuGet-csomag]telepítése | |
| C# parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény] használata ajánlott a Visual Studio Code használatával. |
| C#-szkript (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [meg a bővítmények frissítése]című témakört. |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[Bővítmények frissítése]: ./install-update-binding-extensions-manual.md
[Azure-eszközök bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A functions 1. x alkalmazások automatikusan hivatkoznak a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomagra, 2. x verzióra.

## <a name="next-steps"></a>További lépések

- [Függvény futtatása Service Bus üzenetsor vagy témakör-üzenet létrehozásakor (trigger)](./functions-bindings-service-bus-trigger.md)
- [Azure Service Bus üzenetek küldése Azure Functionsból (kimeneti kötés)](./functions-bindings-service-bus-output.md)
