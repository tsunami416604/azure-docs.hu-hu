---
title: Az Azure Blob tárolási eseményindítója és kötései az Azure Functionshez
description: Ismerje meg az Azure Blob storage-eseményindító és kötések használatát az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277231"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Az Azure Blob storage-kötések az Azure Functionshez – áttekintés

Az Azure Functions [eseményindítók és kötések](./functions-triggers-bindings.md)segítségével integrálható az [Azure Storage szolgáltatással.](https://docs.microsoft.com/azure/storage/) A Blob storage-szal való integráció lehetővé teszi olyan függvények létrehozását, amelyek reagálnak a blobadatok változásaira, valamint az olvasási és írási értékekre.

| Műveletek | Típus |
|---------|---------|
| Függvény futtatása blobtárolási adatok változásaként | [Eseményindító](./functions-bindings-storage-blob-trigger.md) |
| Blob-tárolási adatok olvasása egy függvényben | [Bemeneti kötés](./functions-bindings-storage-blob-input.md) |
| Blob storage-adatok írásának engedélyezése egy függvénynek |[Kimenetkötés](./functions-bindings-storage-blob-output.md) |

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

- [Függvény futtatása blobtárolási adatok változásakor](./functions-bindings-storage-blob-trigger.md)
- [Blob-tárolási adatok olvasása függvény futtatásakor](./functions-bindings-storage-blob-input.md)
- [Blob-tárolási adatok írása egy függvényből](./functions-bindings-storage-blob-output.md)
