---
title: Az Azure Functions HTTP-triggerei és kötései
description: Ismerje meg a HTTP-eseményindítók és kötések használatát az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462105"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Az Azure Functions HTTP-eseményindítók és kötések áttekintése

Az Azure Functions http-kérelmeken keresztül hívható meg kiszolgáló nélküli API-k létrehozásához és [a webhookok megválaszolásához.](https://en.wikipedia.org/wiki/Webhook)

| Műveletek | Típus |
|---------|---------|
| Függvény futtatása HTTP-kérelemből | [Eseményindító](./functions-bindings-http-webhook-trigger.md) |
| HTTP-válasz visszaadása függvényből |[Kimenetkötés](./functions-bindings-http-webhook-output.md) |

A cikkben szereplő kód alapértelmezés szerint .NET Core szintaxis, amelyet a Functions 2.x-es vagy újabb verziója használ. Az 1.x szintaxisról az [1.x függvénysablonokban](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)olvashat.

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
[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[A bővítmények frissítése]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A Functions 1.x alkalmazások automatikusan rendelkeznek egy hivatkozással a [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomag 2.x-es verziójával.

## <a name="next-steps"></a>További lépések

- [Függvény futtatása HTTP-kérelemből](./functions-bindings-http-webhook-trigger.md)
- [HTTP-válasz visszaadása függvényből](./functions-bindings-http-webhook-output.md)
