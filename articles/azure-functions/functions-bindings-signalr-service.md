---
title: Az Azure Functions SignalR szolgáltatás kötései
description: Ismerje meg, hogyan használhatja a SignalR szolgáltatáskötéseket az Azure Functions szolgáltatással.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523036"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-szolgáltatáskötések az Azure Functionshöz

Ez a cikkkészlet bemutatja, hogyan hitelesítheti és küldhet valós idejű üzeneteket az [Azure SignalR-szolgáltatáshoz](https://azure.microsoft.com/services/signalr-service/) kapcsolódó ügyfeleknek az Azure Functions SignalR-szolgáltatáskötéseinek használatával. Az Azure Functions támogatja a SignalR-szolgáltatás bemeneti és kimeneti kötéseit.

| Műveletek | Típus |
|---------|---------|
| A szolgáltatás végpontjának URL-címének és hozzáférési jogkivonatának a visszaadása | [Bemeneti kötés](./functions-bindings-signalr-service-input.md) |
| SignalR szolgáltatás üzenetek küldése |[Kimenetkötés](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Hozzáadás a Functions alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Funkciók 2.x és magasabb

Az eseményindító és a kötések használatához hivatkozni kell a megfelelő csomagra. A NuGet csomag .NET osztálytárakhoz használatos, míg a bővítményköteg az összes többi alkalmazástípushoz használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet csomag]telepítése 3.x verzió | |
| C# Script, Java, JavaScript, Python, PowerShell | A [bővítménycsomag regisztrálása]          | Az [Azure Tools bővítmény] használata ajánlott a Visual Studio-kód használata. |
| C# Script (csak online az Azure Portalon)         | Kötés hozzáadása                            | Ha a meglévő kötésbővítményeket a függvényalkalmazás újbóli közzététele nélkül szeretné frissíteni, olvassa el [a Bővítmények frissítése (Bővítmények frissítése) (Bővítmények frissítése) (Bővítmények frissítése) (Bővítmények frissítése) témakört.] |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[bővítőcsomag]: ./functions-bindings-register.md#extension-bundles
[A bővítmények frissítése]: ./install-update-binding-extensions-manual.md
[Az Azure Tools bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

A SignalR szolgáltatás és az Azure Functions együttes konfigurálásával és használatával kapcsolatos részletekért tekintse meg az [Azure Functions fejlesztését és konfigurálását az Azure SignalR szolgáltatással.](../azure-signalr/signalr-concept-serverless-development-config.md)

### <a name="annotations-library-java-only"></a>Jegyzettár (csak Java)

A SignalR-szolgáltatás jegyzetek Java függvényekben való használatához hozzá kell adnia egy függőséget az *azure-functions-java-library-signalr må±kódhoz* (1.0-s vagy újabb verzió) a *pom.xml* fájlhoz.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>További lépések

- [A szolgáltatás végpontjának URL-címének és hozzáférési jogkivonatának a visszaadása (bemeneti kötés)](./functions-bindings-signalr-service-input.md)
- [SignalR szolgáltatás üzenetek küldése (kimenetkötés)](./functions-bindings-signalr-service-output.md) 
