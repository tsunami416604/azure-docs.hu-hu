---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191036"
---
A kötési attribútumok közvetlenül a function. JSON fájlban vannak definiálva. A kötési típustól függően szükség lehet további tulajdonságokra. A [várólista kimeneti konfigurációja](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) leírja az Azure Storage-várólista kötéséhez szükséges mezőket. A bővítmény megkönnyíti a kötések hozzáadását a function. JSON fájlhoz. 

Kötés létrehozásához kattintson a jobb gombbal (CTRL + kattintás macOS rendszeren) a HttpTrigger mappában található `function.json` fájlra, és válassza a **kötés hozzáadása...** lehetőséget. Kövesse az utasításokat a következő kötési tulajdonságok definiálásához az új kötéshez:

| Kérdés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Kötési irány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Kötés kijelölése iránysal...** | `Azure Queue Storage` | A kötés egy Azure Storage-várólista kötése. |
| **A kódban a kötés azonosítására használt név** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az az üzenetsor, amelybe az üzenet el lesz küldve** | `outqueue` | Annak a sornak a neve, amelyet a kötés ír. Ha a *queueName* nem létezik, a kötés létrehozza az első használatkor. |
| **Válassza a beállítás a következőből: "local. Setting. JSON"** | `AzureWebJobsStorage` | A Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás a Function alkalmazással létrehozott Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazza. |

A function. JSON fájlban egy kötés kerül a `bindings` tömbbe, amelynek a következőhöz hasonlóan kell kinéznie:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::