---
title: Az Azure Service Bus kötései az Azure Functionshez
description: Ismerje meg, hogyan küldhet idát meg az Azure Service Bus eseményindítókat és kötéseket az Azure Functionsben.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277413"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Az Azure Service Bus kötései az Azure Functionshez

Az Azure Functions [eseményindítók és kötések](./functions-triggers-bindings.md)révén integrálható az [Azure Service Bus](https://azure.microsoft.com/services/service-bus) szolgáltatásba. A Service Bus integrálásával olyan funkciókat hozhat létre, amelyek reagálnak a várólistákra vagy a témakörüzenetekre, és várólistákat vagy témaköröket küldenek.

| Műveletek | Típus |
|---------|---------|
| Szolgáltatásbusz-várólista vagy témakörüzenet létrehozásakor funkció futtatása | [Eseményindító](./functions-bindings-service-bus-trigger.md) |
| Azure Service Bus-üzenetek küldése |[Kimenetkötés](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Hozzáadás a Functions alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Funkciók 2.x és magasabb

Az eseményindító és a kötések használatához hivatkozni kell a megfelelő csomagra. A NuGet csomag .NET osztálytárakhoz használatos, míg a bővítményköteg az összes többi alkalmazástípushoz használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet csomag]telepítése 4.x verzió | |
| C# Script, Java, JavaScript, Python, PowerShell | A [bővítménycsomag regisztrálása]          | Az [Azure Tools bővítmény] használata ajánlott a Visual Studio-kód használata. |
| C# Script (csak online az Azure Portalon)         | Kötés hozzáadása                            | Ha a meglévő kötésbővítményeket a függvényalkalmazás újbóli közzététele nélkül szeretné frissíteni, olvassa el [a Bővítmények frissítése (Bővítmények frissítése) (Bővítmények frissítése) (Bővítmények frissítése) (Bővítmények frissítése) témakört.] |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[bővítőcsomag]: ./functions-bindings-register.md#extension-bundles
[A bővítmények frissítése]: ./install-update-binding-extensions-manual.md
[Az Azure Tools bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A Functions 1.x alkalmazások automatikusan rendelkeznek egy hivatkozással a [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomag 2.x-es verziójával.

## <a name="next-steps"></a>További lépések

- [Szolgáltatásbusz-várólista vagy témakörüzenet létrehozásakor egy függvény futtatása (Eseményindító)](./functions-bindings-service-bus-trigger.md)
- [Azure Service Bus-üzenetek küldése az Azure Functions szolgáltatásból (kimenetkötés)](./functions-bindings-service-bus-output.md)
