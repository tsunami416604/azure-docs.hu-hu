---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78201940"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Az indítás során a gazdagép letölti és telepíti a [Storage kötési bővítményt](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) és a Microsoft egyéb kötési bővítményeit. Ez a telepítés azért történik, mert a kötési bővítmények alapértelmezés szerint engedélyezve vannak a *Host. JSON* fájlban a következő tulajdonságokkal:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Ha a kötési bővítményekkel kapcsolatos hibákat észlel, ellenőrizze, hogy a fenti tulajdonságok szerepelnek-e a *Host. JSON*fájlban.
::: zone-end  