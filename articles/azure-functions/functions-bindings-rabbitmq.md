---
title: Azure Functions Azure RabbitMQ-kötések
description: Ismerje meg, hogyan küldhet Azure RabbitMQ-eseményindítókat és-kötéseket Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/11/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: cd6c8de0d9290aab273e25403bb1cb2d8fe07e27
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505726"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Azure Functions RabbitMQ-kötések – áttekintés

> [!NOTE]
> A RabbitMQ-kötések csak a **Windows prémium** csomagokban teljes mértékben támogatottak. A felhasználás és a Linux jelenleg nem támogatott.

A Azure Functions [triggerek és kötések](./functions-triggers-bindings.md)használatával integrálható a [RabbitMQ](https://www.rabbitmq.com/) . A Azure Functions RabbitMQ-kötési bővítmények lehetővé teszik üzenetek küldését és fogadását a RabbitMQ API és a functions használatával.

| Művelet | Típus |
|---------|---------|
| Függvény futtatása, ha egy RabbitMQ-üzenet a várólistán keresztül érkezik | [Eseményindító](./functions-bindings-rabbitmq-trigger.md) |
| RabbitMQ üzenetek küldése |[Kimeneti kötés](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

A bővítmény fejlesztésének megkezdéséhez győződjön meg arról, hogy először [állít be egy RabbitMQ-végpontot](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Ha többet szeretne megtudni a RabbitMQ, tekintse meg az [első lépéseket ismertető oldalt](https://www.rabbitmq.com/getstarted.html).

### <a name="functions-2x-and-higher"></a>Functions 2.x és újabb

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A 4. x verziójú [NuGet-csomag]telepítése | |
| C# parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény] használata ajánlott a Visual Studio Code használatával. |
| C#-szkript (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [meg a bővítmények frissítése]című témakört. |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[Bővítmények frissítése]: ./functions-bindings-register.md
[Azure-eszközök bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A RabbitMQ kötési bővítmények nem támogatottak az 1. x függvényeknél. Használja a 2. x és újabb függvényeket.

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása RabbitMQ-üzenet létrehozásakor (trigger)](./functions-bindings-rabbitmq-trigger.md)
- [RabbitMQ üzenetek küldése Azure Functionsból (kimeneti kötés)](./functions-bindings-rabbitmq-output.md)