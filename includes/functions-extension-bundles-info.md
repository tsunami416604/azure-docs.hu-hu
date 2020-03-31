---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201940"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Az indítás során az állomás letölti és telepíti a [Storage kötésbővítményt](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) és más Microsoft-kötési bővítményeket. A telepítés azért történik, mert a kötésbővítmények alapértelmezés szerint engedélyezve vannak a *host.json* fájlban a következő tulajdonságokkal:
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
> Ha a kötési bővítményekkel kapcsolatos hibákat észlel, ellenőrizze, hogy a fenti tulajdonságok szerepelnek-e a *host.json*könyvtárban.
::: zone-end  