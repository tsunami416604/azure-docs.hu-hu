---
title: Az Azure Event Grid kötései az Azure Functionshez
description: Ismerje meg, hogyan kezelhetők az Event Grid-események az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461079"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Az Azure Event Grid kötései az Azure Functionshez

Ez a hivatkozás bemutatja, hogyan [kezelhetők az Event Grid-események](../event-grid/overview.md) az Azure Functionsben. Az Event Grid-üzenetek HTTP-végponton való kezeléséről az [Események fogadása HTTP-végpontra témakörben](../event-grid/receive-events.md)talál további részleteket.

Az Event Grid egy Azure-szolgáltatás, amely HTTP-kéréseket küld, hogy értesítse önt a *közzétevőkben*bekövetkező eseményekről. A közzétevő az esemény innen származó szolgáltatás vagy erőforrás. Például egy Azure blob storage-fiók egy közzétevő, és [a blob feltöltése vagy törlése egy esemény.](../storage/blobs/storage-blob-event-overview.md) Egyes [Azure-szolgáltatások beépített támogatással rendelkeznek az események Event Grid ben való közzétételéhez.](../event-grid/overview.md#event-sources)

Az *eseménykezelők eseményeket fogadnak* és dolgoznak fel. Az Azure Functions egyike azoknak az [Azure-szolgáltatásoknak, amelyek beépített támogatást nyújtanak az Event Grid-események kezeléséhez.](../event-grid/overview.md#event-handlers) Ebben a hivatkozásban megtanulja az Event Grid eseményindítójának használatát egy függvény meghívására, amikor egy esemény érkezik az Event Gridből, és a kimeneti kötés használatával eseményeket küldhet egy [Eseményrács egyéni témakörbe.](../event-grid/post-to-custom-topic.md)

Ha szeretné, http-eseményindítót használhat az Eseményrács eseményeinek kezeléséhez; lásd: [Események fogadása HTTP-végpontra](../event-grid/receive-events.md). Jelenleg nem használhat Eseményrács-eseményindítót egy Azure Functions-alkalmazáshoz, ha az esemény a [CloudEvents sémában](../event-grid/cloudevents-schema.md#azure-functions)jelenik meg. Ehelyett használjon HTTP-eseményindítót.

| Műveletek | Típus |
|---------|---------|
| Függvény futtatása eseményrács-esemény feladásakor | [Eseményindító](./functions-bindings-event-grid-trigger.md) |
| Eseményrács-esemény küldése |[Kimenetkötés](./functions-bindings-event-grid-output.md) |

A hivatkozásban szereplő kód alapértelmezés szerint .NET Core szintaxis, amelyet a Functions 2.x-es vagy újabb verziója használ. Az 1.x szintaxisról az [1.x függvénysablonokban](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)olvashat.

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
[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[A bővítmények frissítése]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A Functions 1.x alkalmazások automatikusan rendelkeznek egy hivatkozással a [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomag 2.x-es verziójával.

## <a name="next-steps"></a>További lépések
* [Függvény futtatása eseményrács-esemény feladásakor](./functions-bindings-event-grid-trigger.md)
* [Eseményrács-esemény feladása](./functions-bindings-event-grid-trigger.md)
