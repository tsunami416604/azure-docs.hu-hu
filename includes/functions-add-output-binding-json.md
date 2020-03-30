---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191036"
---
A kötési attribútumok közvetlenül a function.json fájlban vannak definiálva. A kötés típusától függően további tulajdonságokra lehet szükség. A [várólista kimeneti konfigurációja](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) az Azure Storage-várólista-kötéshez szükséges mezőket írja le. A kiterjesztés megkönnyíti a kötések hozzáadását a function.json fájlhoz. 

Kötés létrehozásához kattintson a jobb gombbal (Ctrl+click on macOS) a HttpTrigger mappában lévő fájlt, és válassza a `function.json` Kötés **hozzáadása... parancsot.** Az új kötéshez a következő kötési tulajdonságok meghatározásához kövesse az utasításokat:

| Kérdés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Kötésirány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Kötés kiválasztása iránysal...** | `Azure Queue Storage` | A kötés egy Azure Storage-várólista-kötés. |
| **A kötés azonosítására használt név a kódban** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az a várólista, amelyre az üzenetet küldi** | `outqueue` | Annak a várólistának a neve, amelybe a kötés ír. Ha a *queueName* nem létezik, a kötés létrehozza azt az első használatkor. |
| **A "local.setting.json" közül válassza ki a beállítást.** | `AzureWebJobsStorage` | A Storage-fiók kapcsolati karakterláncát tartalmazó alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás a függvényalkalmazással létrehozott Storage-fiók kapcsolati karakterláncát tartalmazza. |

A function.json `bindings` tömbhöz egy kötés kerül hozzáadásra, amely a következőhöz hasonlóan fog kinézni:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::